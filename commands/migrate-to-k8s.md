---
description: Migrate application to Kubernetes with FluxCD GitOps, Istio service mesh, and cloud IAM integration
model: sonnet
argument-hint: [app-name]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task, TodoWrite, AskUserQuestion
---

# Migrate Application to Kubernetes + FluxCD

Migrate an application from serverless/containerized deployment to Kubernetes with:
- **Kustomize base + overlay structure** (multi-environment support)
- **FluxCD image automation** (auto-deploy on new images)
- **External Secrets** integration (Cloud Secrets Manager)
- **Istio service mesh** (VirtualService + Gateway + ExternalDNS)
- **Cloud IAM** (IAM Roles for Service Accounts) for cloud permissions
- **Environment-specific patches** (dev/prod)

## Migration Workflow

This command orchestrates a **7-phase migration** using specialized sub-agents for automated migration, followed by manual traffic management.

**Key Principles:**
- ✅ **Reuse existing cloud resources** - IAM roles and container registries
- ✅ **Only synchronous functions** - Identify HTTP-triggered functions
- ✅ **No new resources if not needed** - Avoid resource duplication
- ✅ **Zero-downtime migration** - Gradual traffic shift (0% → 20% → 50% → 100%)
- ✅ **Instant rollback** - Change Service annotation to revert traffic

### Phase 1: Discovery & Analysis

**Ask the user for application details:**
1. **Application name** (required)
2. **Port** (default: 8080)
3. **Health check endpoint** (default: /health)
4. **Resource size** (small/medium/large or custom CPU/memory)
5. **Dockerfile location** (default: ./Dockerfile)
6. **Container registry name** (default: same as app name)
7. **Cloud services used** (Object Storage, Message Queue, Database, AI Services, etc.)
8. **secrets manager parameter paths** (default: /{app-name}/{env}/)
9. **External dependencies** (MongoDB, Redis, PostgreSQL, etc.)

**Launch Discovery Agent:**
```
Launch a general-purpose agent to analyze:

1. Application Configuration:
   - Read Dockerfile to understand runtime, dependencies, exposed ports
   - Parse environment variables (from .env, .env.example, config files)
   - Identify secrets (API keys, tokens, passwords) → External Secrets
   - Identify public config (URLs, feature flags) → ConfigMap or deployment env
   - Analyze code for cloud SDK usage (for IAM policy requirements)
   - Search for health check endpoints (liveness/readiness probes)

2. Existing IAM Role Detection (CRITICAL):
   - Search for serverless config file in application repository
   - If found, extract IAM role configuration:
     * Role name pattern (e.g., YourApp-${sls:stage}-AccessRole)
     * Existing policies (Object Storage, Message Queue, Database, etc.)
     * Check if Cloud IAM trust policy already exists
   - If serverless config file not found, mark as "needs new Terraform role"

3. container registry Repository Detection (CRITICAL):
   - Search for serverless config file in application repository
   - If found, extract container registry configuration:
     * Service name (from "service:" field)
     * Container registry pattern: serverless-{service}-{stage}
       Example: service=app-example, stage=dev → serverless-app-example-dev
     * Find synchronous function (url: true in functions section)
     * Extract image tag from image.name field
       Example: image.name=basenodeimage → tag is "basenodeimage"
   - If serverless config file not found, mark as "needs new Container registry"
   - IMPORTANT: Only migrate synchronous functions (url: true)

Return structured JSON with:
{
  "runtime": "python|node|go|java",
  "entry_point": "python -m app.main",
  "port": 8080,
  "health_check": "/health",
  "secrets": [
    {"name": "MONGODB_URI", "source": "ssm:/app-name/dev/MONGODB_URI"},
    {"name": "API_KEY", "source": "ssm:/app-name/dev/API_KEY"}
  ],
  "public_config": [
    {"name": "ENV", "value": "dev"},
    {"name": "REGION", "value": "{{REGION}}"}
  ],
  "aws_services": ["s3", "sqs", "bedrock"],
  "dependencies": ["mongodb", "redis"],
  "iam_role": {
    "source": "serverless.com|terraform",
    "role_name": "YourApp-${sls:stage}-AccessRole",
    "has_irsa_trust": true|false,
    "policies": ["s3", "sqs", "kms"]
  },
  "ecr": {
    "source": "serverless.com|terraform",
    "repository": "serverless-app-example-dev",
    "image_tag": "basenodeimage",
    "full_image": "{{ACCOUNT_ID}}.dkr.ecr.{{REGION}}.amazonaws.com/serverless-app-example-dev:basenodeimage"
  }
}
```

### Phase 2: Create Base Kubernetes Manifests

**Launch Manifest Generator Agent:**
```
Create directory: gitops/base/apps/{app-name}/

IMPORTANT: Use container registry configuration from Phase 1 discovery!

Generate the following manifests:
1. namespace.yaml - Namespace with Istio injection enabled
2. serviceaccount.yaml - Cloud IAM placeholder (patched per env)
3. deployment.yaml - Core deployment with:
   - 2 replicas (baseline for dev)
   - Resource requests/limits based on size
   - Pod anti-affinity for multi-AZ distribution
   - Liveness/readiness probes
   - Image from Phase 1 discovery:
     * If existing serverless framework: {ecr.full_image} (e.g., {{ACCOUNT_ID}}.dkr.ecr.{{REGION}}.amazonaws.com/serverless-app-example-dev:basenodeimage)
     * If new app: User-provided image path
   - FluxCD image marker: # {"$imagepolicy": "{app-name}:{app-name}:tag"}
   - Environment variables (public config inline, secrets from ExternalSecret)
4. service.yaml - ClusterIP service on port {port}
   - IMPORTANT: Add Lambda Mesh traffic splitting annotation:
     ```yaml
     annotations:
       traffic.istio.io/weight: "0"  # Start with 0% K8s traffic (100% Lambda)
     ```
   - This enables gradual migration: 0% → 20% → 50% → 100%
5. virtualservice.yaml - Istio routing with ExternalDNS annotation
   - Lambda Mesh CronJob will detect Service annotation and generate weighted routing
   - VirtualService will be managed by Lambda Mesh (auto-updated)
6. externalsecret.yaml - secrets manager integration with $ENVIRONMENT placeholder
   ```yaml
   apiVersion: external-secrets.io/v1
   kind: ExternalSecret
   metadata:
     name: {app-name}-secrets
     namespace: {app-name}
   spec:
     refreshInterval: 1h
     secretStoreRef:
       name: aws-parameter-store
       kind: ClusterSecretStore
     target:
       name: {app-name}-secrets
       creationPolicy: Owner
     data:
       - secretKey: Scontainer registryET_NAME
         remoteRef:
           key: /{app-name}/$ENVIRONMENT/Scontainer registryET_NAME
   ```
   IMPORTANT: Use apiVersion `external-secrets.io/v1` (NOT v1beta1)
7. image-repository.yaml - FluxCD container registry scanning:
   - Repository from Phase 1: {ecr.repository} (e.g., serverless-app-example-dev)
   - Full path: {{ACCOUNT_ID}}.dkr.ecr.{{REGION}}.amazonaws.com/{ecr.repository}
8. image-policy.yaml - Tag selection with digest tracking:
   ```yaml
   apiVersion: image.toolkit.fluxcd.io/v1beta2
   kind: ImagePolicy
   metadata:
     name: {app-name}
     namespace: {app-name}
   spec:
     imageRepositoryRef:
       name: {app-name}
     policy:
       alphabetical:
         order: asc
     filterTags:
       pattern: '^{fixed-tag}$'  # e.g., ^basenextimage$ or ^basenodeimage$
       extract: '$0'
     # Reflect digest for mutable tag (overwrites on every scan)
     digestReflectionPolicy: Always
     interval: 1m
   ```
   IMPORTANT:
   - Use fixed tag pattern from existing serverless framework (e.g., basenextimage, basenodeimage, fmagentsimage)
   - Set `digestReflectionPolicy: Always` for automatic digest updates
   - FluxCD will track {tag}@sha256:... format for immutable deployments
9. image-update-automation.yaml - Auto-update deployment with digest
   ```yaml
   apiVersion: image.toolkit.fluxcd.io/v1beta2
   kind: ImageUpdateAutomation
   metadata:
     name: {app-name}
     namespace: {app-name}
   spec:
     interval: 1m
     sourceRef:
       kind: GitRepository
       name: flux-system
       namespace: flux-system
     git:
       checkout:
         ref:
           branch: main
       commit:
         author:
           email: fluxcdbot@{{YOUR_DOMAIN}}
           name: FluxCD Bot
         messageTemplate: |
           Auto-update {app-name} image

           Objects:
           {{- range $resource, $changes := .Changed.Objects }}
           - {{ $resource.Kind }} {{ $resource.Name }}
             Changes:
           {{- range $_, $change := $changes }}
             {{ $change.OldValue }} -> {{ $change.NewValue }}
           {{- end }}
           {{- end }}

           Automation: {{ .AutomationObject }}
       push:
         branch: main
     update:
       path: ./gitops/base/apps/{app-name}
       strategy: Setters
   ```
   IMPORTANT:
   - Base uses branch: main and updates ./gitops/base/apps/{app-name}
   - Dev overlay will patch branch to 'dev' via inline JSON patch in kustomization.yaml
   - FluxCD writes digest updates (@sha256:...) to base deployment.yaml
   - This works for BOTH fixed tags (basenextimage) and versioned tags (dev-1, dev-2, etc.)
10. kustomization.yaml - Resource list
11. README.md - Documentation with architecture, monitoring, troubleshooting

Use templates from agent instructions (lines 78-310).
```

### Phase 3: Create Environment Overlays

**Launch Overlay Generator Agent:**
```
Create directories:
- gitops/overlays/dev/apps/{app-name}/
- gitops/overlays/prod/apps/{app-name}/

For each environment, generate patches:
1. kustomization.yaml - References base + patches
2. deployment-patch.yaml - Environment-specific values (replicas, env vars)
3. externalsecret.yaml - Replace $ENVIRONMENT with dev/prod
   IMPORTANT: Must use apiVersion `external-secrets.io/v1` (NOT v1beta1)
4. virtualservice.yaml - Environment-specific domain
5. serviceaccount-irsa.yaml - Cloud IAM role ARN for environment (if NEW Terraform role)

IMPORTANT: DO NOT create separate image-policy.yaml or image-update-automation.yaml files in overlays!
Instead, add inline JSON patches to gitops/overlays/{env}/apps/kustomization.yaml (Phase 5)

Dev overlay configuration:
- Domain: dev-{app-name}.{{YOUR_DOMAIN}}
- Replicas: 2
- secrets manager path: /{app-name}/dev/
- Cloud IAM role: arn:aws:iam::{{ACCOUNT_ID}}:role/dev-{app-name}
- Container registry: {ecr-repo}-dev (already in base, no patch needed)
- Image tag: Fixed tag from existing serverless framework (e.g., basenextimage)
- ImageUpdateAutomation: Inline patch in kustomization.yaml to change branch main → dev

Prod overlay configuration:
- Domain: {app-name}.{{YOUR_DOMAIN}}
- Replicas: 3
- secrets manager path: /{app-name}/prod/
- Cloud IAM role: arn:aws:iam::{{ACCOUNT_ID}}:role/prod-{app-name}
- Container registry: {ecr-repo}-prod (needs image-repository.yaml patch)
- Image tag: Fixed tag from existing serverless framework (same as dev, e.g., basenextimage)
- ImageUpdateAutomation: No patch needed (already uses branch: main)

Use templates from agent instructions (lines 313-453).
```

### Phase 4: Terraform IAM Configuration

**Launch Terraform IAM Agent:**
```
IMPORTANT: Check if application has existing existing serverless framework IAM role first!

**Step 1: Detect Existing IAM Role (existing serverless framework pattern)**

Search for serverless config file in the original application repository:
- Look for IAM role definition in resources.Resources section
- Common pattern: {AppName}-${sls:stage}-AccessRole
- Example: YourApp-dev-AccessRole, YourApp-prod-AccessRole

If found, check if role already has Cloud IAM trust policy:
- Principal.Federated: arn:aws:iam::{{ACCOUNT_ID}}:oidc-provider/oidc.eks.{{REGION}}.amazonaws.com/id/...
- Condition.StringEquals with EKS OIDC provider and service account

**Step 2A: REUSE Existing Role (if existing serverless framework role exists)**

If IAM role exists in serverless config file:

1. Extract role name pattern from serverless config file:
   Example: YourApp-${sls:stage}-AccessRole → YourApp-dev-AccessRole / YourApp-prod-AccessRole

2. Update overlay kustomization.yaml with inline patch (NO separate file):
   ```yaml
   # gitops/overlays/dev/apps/kustomization.yaml
   patches:
     - patch: |-
         apiVersion: v1
         kind: ServiceAccount
         metadata:
           name: {app-name}
           namespace: {app-name}
           annotations:
             eks.amazonaws.com/role-arn: "arn:aws:iam::{{ACCOUNT_ID}}:role/{RoleName}-dev-AccessRole"
       target:
         kind: ServiceAccount
         name: {app-name}
         namespace: {app-name}
   ```

3. Verify existing role has Cloud IAM trust policy:
   - Check serverless config file AssumeRolePolicyDocument
   - If missing Cloud IAM trust policy, add it:
     ```yaml
     - Effect: Allow
       Principal:
         Federated:
           Fn::Sub: "arn:aws:iam::${cloud::AccountId}:oidc-provider/oidc.eks.{{REGION}}.amazonaws.com/id/6F79B1719917D85AE8EEF3E8982F7CD1"
       Action: sts:AssumeRoleWithWebIdentity
       Condition:
         StringEquals:
           "oidc.eks.{{REGION}}.amazonaws.com/id/6F79B1719917D85AE8EEF3E8982F7CD1:sub": "system:serviceaccount:{app-name}:{app-name}"
           "oidc.eks.{{REGION}}.amazonaws.com/id/6F79B1719917D85AE8EEF3E8982F7CD1:aud": "sts.amazonaws.com"
     ```

4. NO Terraform resources needed - role already exists!

**Step 2B: CREATE New Role (if NO existing serverless framework role)**

If no IAM role exists (like app-name):

1. Create IAM policy JSON (policies/{app-name}-policy.json):
   - Object Storage permissions (GetObject, PutObject, DeleteObject, ListBucket)
   - Message Queue permissions (SendMessage, ReceiveMessage, DeleteMessage, GetQueueAttributes)
   - Database permissions (GetItem, PutItem, Query, Scan)
   - AI Services permissions (InvokeModel, InvokeModelWithResponseStream)
   - Secrets Manager permissions (GetSecretValue)
   - KMS permissions (Decrypt, Encrypt)

   Use ${var.environment} for resource ARNs to support both dev and prod.

2. Add Terraform resources in main.tf:
   ```hcl
   # IAM Policy for {app-name}
   resource "aws_iam_policy" "{app_name}" {
     name        = "${var.environment}-{app-name}"
     description = "IAM policy for {app-name} service account"
     policy      = templatefile("${path.module}/policies/{app-name}-policy.json", {
       environment = var.environment
     })
   }

   # Cloud IAM Role for {app-name}
   module "irsa_{app_name}" {
     source  = "terraform-aws-modules/iam/aws//modules/iam-role-for-service-accounts-eks"
     version = "~> 5.0"

     role_name = "${var.environment}-{app-name}"

     role_policy_arns = {
       policy = aws_iam_policy.{app_name}.arn
     }

     oidc_providers = {
       main = {
         provider_arn               = module.eks.oidc_provider_arn
         namespace_service_accounts = ["system:serviceaccount:{app-name}:{app-name}"]
       }
     }

     tags = merge(var.tags, {
       Name = "${var.environment}-{app-name}"
     })
   }
   ```

3. Add Terraform outputs in outputs.tf:
   ```hcl
   output "{app_name}_irsa_role_arn" {
     description = "ARN of the Cloud IAM role for {app-name}"
     value       = module.irsa_{app_name}.iam_role_arn
   }
   ```

4. Create separate Cloud IAM patch file (gitops/overlays/dev/apps/{app-name}/serviceaccount-irsa.yaml):
   ```yaml
   apiVersion: v1
   kind: ServiceAccount
   metadata:
     name: {app-name}
     namespace: {app-name}
     annotations:
       eks.amazonaws.com/role-arn: "arn:aws:iam::{{ACCOUNT_ID}}:role/dev-{app-name}"
   ```

5. Reference patch in overlay kustomization.yaml:
   ```yaml
   patches:
     - path: {app-name}/serviceaccount-irsa.yaml
       target:
         kind: ServiceAccount
         name: {app-name}
         namespace: {app-name}
   ```

**Step 3: container registry Repository Configuration (NO Terraform needed)**

IMPORTANT: DO NOT create Container registry - use existing existing serverless framework repository!

If serverless config file exists (from Phase 1 discovery):
- Container registry already exists: serverless-{service}-{stage}
- Use existing image tag from Phase 1: {image.name}
- Full image path: {{ACCOUNT_ID}}.dkr.ecr.{{REGION}}.amazonaws.com/serverless-{service}-{stage}:{image.name}
- Update deployment.yaml and image-repository.yaml with discovered values
- NO Terraform resources needed

If serverless config file does NOT exist (new application):
- Ask user if Container registry already exists
- If yes, get repository name and tag from user
- If no, create aws_ecr_repository in main.tf (rare case)

**Summary:**
- ✅ existing serverless framework role exists → Inline Cloud IAM patch in kustomization.yaml (like existing apps)
- ✅ No existing serverless framework role → Terraform Cloud IAM + separate patch file (like app-name)
- ✅ existing serverless framework container registry exists → Use existing repository (common case)
- ⚠️ No Container registry → Create new Terraform container registry (rare case)
```

### Phase 5: Update FluxCD Kustomization

**Launch FluxCD Integration Agent:**
```
Update FluxCD kustomization files to include new app and add inline patches:

1. Edit gitops/overlays/dev/apps/kustomization.yaml:
   a) Add resource: {app-name}

   b) Add inline ImageUpdateAutomation patch to change branch from main → dev:
      ```yaml
      patches:
        # {app-name}: ImageUpdateAutomation to use dev branch
        - patch: |-
            - op: replace
              path: /spec/git/checkout/ref/branch
              value: dev
            - op: replace
              path: /spec/git/push/branch
              value: dev
          target:
            kind: ImageUpdateAutomation
            name: {app-name}
            namespace: {app-name}
      ```

2. Edit gitops/overlays/prod/apps/kustomization.yaml:
   a) Add resource: {app-name}

   b) Add ImageRepository patch to use -prod container registry:
      ```yaml
      patches:
        # {app-name}: ImageRepository to use prod container registry
        - path: {app-name}/image-repository.yaml
          target:
            kind: ImageRepository
            name: {app-name}
            namespace: {app-name}
      ```

   c) NO ImageUpdateAutomation patch needed (already uses branch: main in base)

3. Create gitops/overlays/prod/apps/{app-name}/image-repository.yaml:
   ```yaml
   apiVersion: image.toolkit.fluxcd.io/v1beta2
   kind: ImageRepository
   metadata:
     name: {app-name}
     namespace: {app-name}
   spec:
     image: {{ACCOUNT_ID}}.dkr.ecr.{{REGION}}.amazonaws.com/{ecr-repo}-prod
   ```

Verify references are correct (relative paths).
```

### Phase 6: Validation & Testing

**Launch Validation Agent:**
```
Run comprehensive validation checks:

0. Verify cluster prerequisites:
   - kubectl get crd externalsecrets.external-secrets.io
   - kubectl get clustersecretstore aws-parameter-store
   - If missing, report error and stop (External Secrets Operator required)

1. Test Kustomize build locally:
   - kubectl kustomize gitops/overlays/dev
   - kubectl kustomize gitops/overlays/prod
   - Check for YAML syntax errors
   - Verify all ExternalSecrets use apiVersion: external-secrets.io/v1

2. Validate YAML syntax (client-side only, no cluster access required):
   - kubectl apply --dry-run=client -k gitops/overlays/dev
   - kubectl apply --dry-run=client -k gitops/overlays/prod
   - IMPORTANT: Use --dry-run=client (NOT server) to avoid cluster dependency errors

3. Check secrets manager parameters exist:
   - aws ssm describe-parameters --parameter-filters "Key=Name,Option=BeginsWith,Values=/{app-name}/dev/"
   - List all required secrets from Phase 1
   - Report missing parameters

4. Deploy Terraform (Cloud IAM roles):
   - terraform plan -var-file=dev.tfvars
   - If user approves, terraform apply -var-file=dev.tfvars
   - Verify role created: terraform output {app_name}_irsa_role_arn

5. Commit and push changes:
   - git add gitops/ main.tf policies/ outputs.tf
   - git commit -m "Add {app-name} Kubernetes deployment with FluxCD"
   - git push origin dev

6. Monitor FluxCD reconciliation:
   - flux reconcile kustomization flux-system
   - kubectl get kustomization -n flux-system -w (30 seconds)
   - kubectl get namespace {app-name}
   - kubectl get pods -n {app-name} -w (60 seconds)
   - kubectl get externalsecret -n {app-name}
   - kubectl describe externalsecret -n {app-name} {app-name}-secrets
   - kubectl get virtualservice -n {app-name}

7. Test application access:
   - nslookup dev-{app-name}.{{YOUR_DOMAIN}}
   - curl -I https://dev-{app-name}.{{YOUR_DOMAIN}}
   - kubectl logs -n {app-name} -l app={app-name} --tail=100
   - Verify Istio sidecar injected: kubectl get pod -n {app-name} -o jsonpath='{.items[0].spec.containers[*].name}'

Report validation results to user.
```

### Phase 7: Gradual Traffic Migration (Lambda → Kubernetes)

**IMPORTANT: This phase enables zero-downtime migration using Lambda Mesh traffic splitting**

**Prerequisites:**
- Lambda function must have environment variables:
  - `LAMBDA_ISTIO_ENABLED="true"`
  - `LAMBDA_ISTIO_DOMAIN="{domain}.{{YOUR_DOMAIN}}"`
- Lambda Mesh CronJob is running and has discovered the Lambda function

**Traffic Migration Strategy:**
```
Step 1: Verify 100% Lambda Traffic (Initial State)
- Service annotation: traffic.istio.io/weight: "0"
- Lambda Mesh generates VirtualService with 100% Lambda weight
- Test: curl -I https://dev-{app-name}.{{YOUR_DOMAIN}} | grep x-backend-source
  Expected: x-backend-source: lambda

Step 2: Canary Test - 20% Kubernetes Traffic
- Update Service annotation: traffic.istio.io/weight: "20"
- kubectl patch service {app-name} -n {app-name} --type merge -p '{"metadata":{"annotations":{"traffic.istio.io/weight":"20"}}}'
- Wait for Lambda Mesh CronJob sync (max 1 hour, or trigger manually)
- Lambda Mesh updates VirtualService: 80% Lambda, 20% K8s
- Monitor logs and metrics for 1-2 hours
- Test distribution:
  for i in {1..10}; do curl -I https://dev-{app-name}.{{YOUR_DOMAIN}} | grep x-backend-source; done
  Expected: ~2 kubernetes, ~8 lambda

Step 3: Increase to 50% Kubernetes Traffic
- Update Service annotation: traffic.istio.io/weight: "50"
- Wait for Lambda Mesh sync
- VirtualService: 50% Lambda, 50% K8s
- Monitor for 2-4 hours

Step 4: Increase to 100% Kubernetes Traffic
- Update Service annotation: traffic.istio.io/weight: "100"
- Wait for Lambda Mesh sync
- VirtualService: 0% Lambda, 100% K8s
- Monitor for 24 hours

Step 5: Decommission Lambda Function (Optional)
- Once confident with K8s deployment, disable Lambda:
  - Remove LAMBDA_ISTIO_ENABLED from Lambda environment variables
  - Lambda Mesh will stop managing the VirtualService
  - Keep Lambda deployed for emergency rollback
```

**Rollback Strategy:**
```
If issues detected at any stage:
1. Immediately reduce traffic weight to previous safe level
   kubectl patch service {app-name} -n {app-name} --type merge -p '{"metadata":{"annotations":{"traffic.istio.io/weight":"0"}}}'
2. Wait for Lambda Mesh sync (or trigger manually)
3. Investigate K8s deployment issues
4. Fix and retry canary deployment
```

**Monitoring Commands:**
```bash
# Check Lambda Mesh CronJob status
kubectl get cronjob -n lambda-mesh
kubectl get jobs -n lambda-mesh --sort-by=.status.startTime | tail -5

# View Lambda Mesh logs (latest run)
kubectl logs -n lambda-mesh job/$(kubectl get jobs -n lambda-mesh -o jsonpath='{.items[-1].metadata.name}')

# Check VirtualService traffic weights
kubectl get virtualservice -n {app-name} {app-name} -o yaml | grep -A 10 weight

# Test traffic distribution
for i in {1..20}; do curl -s -I https://dev-{app-name}.{{YOUR_DOMAIN}} | grep x-backend-source; done | sort | uniq -c

# Monitor K8s pod health
kubectl get pods -n {app-name} -w
kubectl logs -n {app-name} -l app={app-name} -f

# Check Lambda invocations (should decrease as K8s weight increases)
aws lambda get-function --function-name {lambda-name} --region {{REGION}}
aws cloudwatch get-metric-statistics --namespace cloud/Lambda \
  --metric-name Invocations --dimensions Name=FunctionName,Value={lambda-name} \
  --start-time $(date -u -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) \
  --end-time $(date -u +%Y-%m-%dT%H:%M:%S) \
  --period 300 --statistics Sum
```

**Manual Lambda Mesh Trigger (if needed):**
```bash
# Force Lambda Mesh sync without waiting for CronJob
kubectl create job --from=cronjob/lambda-sync lambda-sync-manual-$(date +%s) -n lambda-mesh

# Watch job completion
kubectl get jobs -n lambda-mesh -w
```

## Todo Management

Throughout the migration, **use TodoWrite tool** to track progress:

**Initial Todo List (after Phase 1):**
```
1. Discover application details (config, IAM role, Container registry)
2. Create base Kubernetes manifests (with Lambda Mesh annotation)
3. Create dev environment overlay
4. Create prod environment overlay
5. Configure IAM (reuse existing serverless framework role OR create Terraform Cloud IAM)
6. Update FluxCD kustomization files
7. Validate and test deployment (0% K8s traffic)
8. Gradual traffic migration (20% → 50% → 100% K8s)
```

**Note:** Phase 7-8 are manual steps performed AFTER automated deployment. The command will:
- Complete Phases 1-6 automatically with sub-agents
- Deploy application to K8s with 0% traffic weight
- Provide instructions for gradual traffic migration (Phase 7)

**Update todo status as each phase completes:**
- Mark as `in_progress` when starting a phase
- Mark as `completed` immediately after finishing
- Add new todos if blockers discovered (e.g., "Create missing secrets manager parameters")

## User Interaction Guidelines

**ALWAYS ask the user for confirmation before:**
1. Applying Terraform changes (show plan output first)
2. Pushing to git (show files changed)
3. Creating cloud resources (container registry, IAM roles)
4. Deploying to production environment

**NEVER assume:**
- Application port (ask if not in Dockerfile)
- Health check endpoint (ask if not found in code)
- Resource requirements (ask or use small as default)
- secrets manager parameter paths (ask if non-standard)
- IAM role strategy (ALWAYS check for existing existing serverless framework role first!)
- Container registry (ALWAYS check for existing existing serverless framework container registry first!)

**CRITICAL: IAM Role Strategy**
ALWAYS follow this decision tree in Phase 4:
1. Search for serverless config file in application repository
2. If found → Extract role name → Use inline patch in kustomization.yaml (NO Terraform)
3. If NOT found → Create new Terraform Cloud IAM role + separate patch file

Examples:
- ✅ Existing app has serverless config file → Uses YourApp-dev-AccessRole (inline patch)
- ✅ app-name has NO serverless config file → Uses dev-app-name (Terraform + patch file)

**CRITICAL: container registry Repository Strategy**
ALWAYS follow this decision tree in Phase 1:
1. Search for serverless config file in application repository
2. If found → Extract container registry configuration:
   - Service name from "service:" field
   - Repository: serverless-{service}-{stage}
   - Find function with "url: true" (synchronous Lambda)
   - Extract image tag from "image.name" field
3. If NOT found → Ask user if container registry exists or needs creation

Examples:
- ✅ app-example has serverless config file → Uses serverless-app-example-dev:basenodeimage (existing container registry)
- ✅ New app → Ask user for container registry details or create new repository

**CRITICAL: Lambda Mesh Traffic Splitting**
ALWAYS add traffic annotation to Service (Phase 2):
1. Initial deployment: traffic.istio.io/weight: "0" (100% Lambda)
2. After validation: Provide Phase 7 instructions for gradual migration
3. User manually increases weight: 0% → 20% → 50% → 100%
4. Lambda Mesh CronJob auto-updates VirtualService with weights

**Progress Communication:**
- Update todo list after each phase
- Show command output for validation steps
- Report errors immediately with troubleshooting steps
- Provide summary at end with verification checklist

## Migration Checklist

After completing all phases, verify:

- [ ] Base manifests created in `gitops/base/apps/{app-name}/`
- [ ] Dev overlay created in `gitops/overlays/dev/apps/{app-name}/`
- [ ] Prod overlay created in `gitops/overlays/prod/apps/{app-name}/`
- [ ] IAM role configured:
  - [ ] **If existing serverless framework role exists**: Inline Cloud IAM patch added to kustomization.yaml (NO Terraform)
  - [ ] **If NO existing serverless framework role**: IAM policy created in `policies/{app-name}-policy.json` + Terraform Cloud IAM module + separate patch file
- [ ] secrets manager parameters exist for all secrets
- [ ] Kustomize builds successfully for dev and prod
- [ ] Terraform applied (if new Cloud IAM role was created)
- [ ] Git committed and pushed to dev branch
- [ ] FluxCD reconciled successfully
- [ ] Pods running in namespace
- [ ] ExternalSecret synced secrets
- [ ] DNS record created (ExternalDNS)
- [ ] Application accessible via HTTPS
- [ ] Istio sidecar injected
- [ ] Logs show healthy application
- [ ] Service has traffic.istio.io/weight annotation set to "0"
- [ ] Lambda function has LAMBDA_ISTIO_ENABLED="true" environment variable
- [ ] Traffic is 100% Lambda (x-backend-source: lambda)

**Phase 7 - Traffic Migration Checklist (Manual Steps):**
- [ ] Start with 20% K8s traffic - Monitor for 1-2 hours
- [ ] Increase to 50% K8s traffic - Monitor for 2-4 hours
- [ ] Increase to 100% K8s traffic - Monitor for 24 hours
- [ ] Verify Lambda invocations decreased to zero
- [ ] (Optional) Decommission Lambda function

## Common Issues & Solutions

**Issue: "no matches for kind ExternalSecret in version external-secrets.io/v1beta1"**
- **Cause**: Generated manifests use deprecated v1beta1 API version
- **Solution**:
  ```bash
  # Check which API version is preferred by the cluster
  kubectl api-resources | grep externalsecret
  # Should show: external-secrets.io/v1

  # Update all ExternalSecret manifests to use v1
  find gitops -name "externalsecret.yaml" -exec sed -i '' 's|external-secrets.io/v1beta1|external-secrets.io/v1|g' {} \;

  # Verify the change
  grep -r "apiVersion.*external-secrets" gitops/

  # Rebuild and test
  kubectl kustomize gitops/overlays/dev
  ```
- **Prevention**: The command now explicitly generates `external-secrets.io/v1` (not v1beta1)

**Issue: Pods stuck in Pending state**
- Check NodePool capacity: `kubectl get nodepools`
- Check pod events: `kubectl describe pod -n {app-name} <pod-name>`
- Verify resource requests don't exceed NodePool limits

**Issue: ExternalSecret not syncing**
- Check secrets manager parameters exist: `aws ssm get-parameters-by-path --path /{app-name}/dev/`
- Verify ClusterSecretStore exists: `kubectl get clustersecretstore`
- Check External Secrets Operator logs: `kubectl logs -n external-secrets -l app.kubernetes.io/name=external-secrets`

**Issue: DNS not resolving**
- Check VirtualService has annotation: `kubectl get vs -n {app-name} -o yaml | grep external-dns`
- Verify ExternalDNS is running: `kubectl get pods -n external-dns`
- Check ExternalDNS logs: `kubectl logs -n external-dns -l app.kubernetes.io/name=external-dns`

**Issue: FluxCD not auto-updating image**
- Check ImageRepository scanning: `kubectl describe imagerepository -n {app-name}`
- Verify ImagePolicy matches tags: `kubectl describe imagepolicy -n {app-name}`
- Check image-reflector logs: `kubectl logs -n flux-system deployment/image-reflector-controller`

**Issue: 503 errors from Istio**
- Check Service selector matches Pod labels
- Verify VirtualService references correct service
- Check Istio gateway exists: `kubectl get gateway -n istio-system main-gateway`

**Issue: Pods failing with cloud permissions errors (AccessDenied, 403)**
- **Check Cloud IAM role annotation**: `kubectl get sa -n {app-name} {app-name} -o yaml | grep role-arn`
- **Verify role exists in cloud**:
  - If using existing serverless framework role: `aws iam get-role --role-name YourApp-dev-AccessRole`
  - If using Terraform role: `aws iam get-role --role-name dev-{app-name}`
- **Check Cloud IAM trust policy**:
  ```bash
  aws iam get-role --role-name <role-name> --query 'Role.AssumeRolePolicyDocument'
  ```
  Must include:
  - Federated: `arn:aws:iam::{{ACCOUNT_ID}}:oidc-provider/oidc.eks.{{REGION}}.amazonaws.com/id/6F79B1719917D85AE8EEF3E8982F7CD1`
  - StringEquals condition with correct namespace and service account
- **Verify IAM policies attached**:
  ```bash
  aws iam list-attached-role-policies --role-name <role-name>
  aws iam list-role-policies --role-name <role-name>
  ```
- **Check pod environment variables**:
  ```bash
  kubectl exec -n {app-name} <pod-name> -- env | grep cloud
  ```
  Should see: `cloud_ROLE_ARN`, `cloud_WEB_IDENTITY_TOKEN_FILE`

**Issue: Wrong IAM role type used**
- **Symptom**: Created Terraform Cloud IAM role but app already has existing serverless framework role (wasted resources)
- **Solution**: Delete Terraform resources, switch to inline patch:
  ```bash
  # Remove from main.tf and outputs.tf
  terraform state rm module.irsa_{app_name}
  terraform state rm aws_iam_policy.{app_name}

  # Update kustomization.yaml with inline patch (see Phase 4 Step 2A)
  ```

**Issue: Lambda Mesh not routing traffic to K8s (all traffic goes to Lambda)**
- **Check Service annotation**:
  ```bash
  kubectl get service -n {app-name} {app-name} -o yaml | grep traffic.istio.io/weight
  ```
  Should show annotation with weight value
- **Check Lambda Mesh CronJob running**:
  ```bash
  kubectl get cronjob -n lambda-mesh
  kubectl get jobs -n lambda-mesh --sort-by=.status.startTime | tail -3
  ```
- **Check Lambda Mesh logs**:
  ```bash
  kubectl logs -n lambda-mesh job/<latest-job> | grep {app-name}
  ```
- **Force Lambda Mesh sync**:
  ```bash
  kubectl create job --from=cronjob/lambda-sync lambda-sync-manual-$(date +%s) -n lambda-mesh
  ```
- **Verify VirtualService generated**:
  ```bash
  kubectl get virtualservice -n {app-name} -o yaml | grep -A 20 "weight:"
  ```
  Should show weighted routing with both Lambda and K8s destinations

**Issue: Traffic not splitting as expected**
- **Test actual distribution**:
  ```bash
  for i in {1..20}; do curl -s -I https://dev-{app-name}.{{YOUR_DOMAIN}} | grep x-backend-source; done | sort | uniq -c
  ```
  Should match annotation weight (e.g., 20% weight → ~4 kubernetes, ~16 lambda)
- **Check Lambda function still has LAMBDA_ISTIO_ENABLED**:
  ```bash
  aws lambda get-function-configuration --function-name {lambda-name} --region {{REGION}} --query 'Environment.Variables'
  ```
- **Check Lambda Mesh discovered Lambda**:
  ```bash
  kubectl logs -n lambda-mesh job/<latest-job> | grep "LAMBDA_ISTIO_ENABLED"
  ```

**Issue: Cannot rollback to Lambda (K8s has issues)**
- **Immediate rollback**:
  ```bash
  # Set traffic to 0% K8s (100% Lambda)
  kubectl patch service {app-name} -n {app-name} --type merge -p '{"metadata":{"annotations":{"traffic.istio.io/weight":"0"}}}'

  # Force Lambda Mesh sync
  kubectl create job --from=cronjob/lambda-sync lambda-sync-manual-$(date +%s) -n lambda-mesh

  # Wait for sync (watch VirtualService)
  watch kubectl get virtualservice -n {app-name} {app-name} -o yaml
  ```
- **Verify rollback**:
  ```bash
  curl -I https://dev-{app-name}.{{YOUR_DOMAIN}} | grep x-backend-source
  # Should show: x-backend-source: lambda
  ```

## Best Practices

1. **Use correct API versions** - ALWAYS use `external-secrets.io/v1` for ExternalSecret (NOT v1beta1)
2. **ALWAYS check for existing serverless framework resources first** - Reuse existing IAM roles and container registry repositories
3. **Only migrate synchronous Lambda functions** - Look for `url: true` in serverless config file
4. **Start with 0% K8s traffic** - Deploy with `traffic.istio.io/weight: "0"` annotation
5. **Gradual traffic migration** - Use Lambda Mesh for zero-downtime: 0% → 20% → 50% → 100%
6. **Monitor at each step** - Wait 1-2 hours at 20%, 2-4 hours at 50%, 24 hours at 100%
7. **Keep Lambda for rollback** - Don't decommission Lambda until fully confident in K8s
8. **Keep base manifests generic** - Use overlays for env-specific values
9. **Document all environment variables** - Update README with table
10. **Use health probes** - Ensure proper readiness/liveness checks
11. **Set resource limits** - Prevent resource starvation
12. **Enable pod anti-affinity** - Improve availability across AZs
13. **Test locally first** - `kubectl kustomize` before pushing with `--dry-run=client`
14. **Incremental rollout** - Deploy to dev → verify → deploy to prod
15. **Monitor FluxCD reconciliation** - Watch for errors during sync
16. **Version control everything** - Commit Terraform and K8s manifests together

### Decision Trees (Best Practices)

#### IAM Role Strategy

```
Is there a serverless config file in the app repository?
│
├─ YES → Does it define an IAM role?
│         │
│         ├─ YES → REUSE existing role
│         │        ├─ Verify Cloud IAM trust policy exists
│         │        ├─ Use inline patch in kustomization.yaml
│         │        └─ NO Terraform resources needed
│         │
│         └─ NO → CREATE new Terraform Cloud IAM role
│                  ├─ Create IAM policy JSON
│                  ├─ Add Terraform module
│                  └─ Use separate patch file
│
└─ NO → CREATE new Terraform Cloud IAM role
         ├─ Create IAM policy JSON
         ├─ Add Terraform module
         └─ Use separate patch file
```

#### container registry Repository Strategy

```
Is there a serverless config file in the app repository?
│
├─ YES → Extract container registry configuration
│         ├─ Service name: from "service:" field
│         ├─ Repository: serverless-{service}-{stage}
│         ├─ Find function with "url: true" (synchronous)
│         ├─ Image tag: from "image.name" field
│         ├─ Use existing Container registry
│         └─ NO Terraform resources needed
│
└─ NO → Ask user about container registry
         ├─ Does Container registry exist?
         │   ├─ YES → Get repository name and tag from user
         │   └─ NO → CREATE new Terraform container registry (rare)
         └─ Update manifests with container registry details
```