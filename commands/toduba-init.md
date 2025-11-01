---
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
argument-hint: "[--force] [--verbose]"
description: "Analizza il progetto e genera documentazione completa in /docs"
---

# Toduba Init V2.0 - Smart Documentation Generator 📚

## Obiettivo

Analizzare il progetto, rilevare automaticamente la struttura (monorepo vs single service), identificare i servizi presenti e generare documentazione completa e organizzata seguendo la nuova struttura gerarchica.

## Argomenti

- `--force`: Rigenera completamente la documentazione anche se esiste
- `--verbose`: Output dettagliato durante la generazione

Argomenti ricevuti: $ARGUMENTS

## Struttura Documentazione Generata

```
docs/
├── .toduba-meta/              # Metadata e cache (JSON)
│   ├── project-type.json      # Tipo progetto rilevato
│   ├── services.json          # Lista servizi e metadati
│   └── last-update.json       # Info ultimo aggiornamento
│
├── global/                    # Documentazione globale progetto
│   ├── README.md             # Overview progetto completo
│   ├── ARCHITECTURE.md       # Architettura generale sistema
│   ├── SETUP.md              # Setup globale (se monorepo)
│   ├── CONTRIBUTING.md       # Linee guida contribuzione
│   └── adr/                  # Architecture Decision Records
│       ├── 0001-template.md  # Template per nuove ADR
│       └── README.md         # Indice ADR
│
├── services/                  # SEMPRE presente (1+ servizi)
│   └── [service-name]/       # Es: app, backend, frontend, api
│       ├── README.md         # Overview servizio (Tier 1)
│       ├── SETUP.md          # Setup specifico servizio (Tier 1)
│       ├── ARCHITECTURE.md   # Architettura servizio (Tier 1)
│       ├── TECH-STACK.md     # Stack tecnologico (Tier 1)
│       ├── STYLE-GUIDE.md    # Convenzioni codice (Tier 1)
│       ├── ENDPOINTS.md      # API endpoints (Tier 2, solo backend/api)
│       ├── DATABASE.md       # Schema database (Tier 2, solo se DB)
│       ├── TESTING.md        # Strategia testing (Tier 2)
│       └── TROUBLESHOOTING.md # FAQ e problemi comuni (Tier 2)
│
└── operations/                # DevOps e operations
    ├── DEPLOYMENT.md         # Procedure deployment
    ├── CI-CD.md              # Pipeline CI/CD
    ├── MONITORING.md         # Logging e monitoring
    ├── SECURITY.md           # Security guidelines
    └── ENVIRONMENT-VARS.md   # Configurazione environment
```

## 🔄 Processo di Generazione

### STEP 1: Verifica Stato Attuale

```bash
# Controlla se docs esiste
if [ -d "docs" ] && [ "$FORCE" != "true" ]; then
  echo "⚠️  Documentazione esistente trovata."
  echo "   Usa --force per rigenerare o /toduba-update-docs per aggiornamenti incrementali"

  # Verifica metadata
  if [ -f "docs/.toduba-meta/last-update.json" ]; then
    echo "   Ultimo aggiornamento: $(cat docs/.toduba-meta/last-update.json | grep timestamp)"
  fi

  exit 0
fi

# Se --force, backup documentazione esistente
if [ -d "docs" ] && [ "$FORCE" == "true" ]; then
  timestamp=$(date +%Y%m%d_%H%M%S)
  mv docs "docs.backup.$timestamp"
  echo "📦 Backup creato: docs.backup.$timestamp"
fi
```

### STEP 2: Analisi Progetto (Auto-Detection)

#### 2.1 Rilevamento Tipo Progetto

```bash
PROJECT_TYPE="single_service"
SERVICES=()

# Cerca indicatori monorepo
if [ -f "pnpm-workspace.yaml" ] || [ -f "lerna.json" ] || [ -f "nx.json" ]; then
  PROJECT_TYPE="monorepo"
elif grep -q "\"workspaces\"" package.json 2>/dev/null; then
  PROJECT_TYPE="monorepo"
fi

# Conta directory con package.json (o altri config files)
PACKAGE_JSON_COUNT=$(find . -name "package.json" -not -path "*/node_modules/*" | wc -l)
if [ $PACKAGE_JSON_COUNT -gt 1 ]; then
  PROJECT_TYPE="monorepo"
fi
```

#### 2.2 Rilevamento Servizi

**Strategia**: Cerca directory con file di configurazione (package.json, pubspec.yaml, go.mod, etc.)

```bash
# Trova tutti i potenziali servizi
find_services() {
  local services=()

  # Node.js/TypeScript projects
  for pkg in $(find . -name "package.json" -not -path "*/node_modules/*" -not -path "*/dist/*"); do
    service_path=$(dirname "$pkg")
    service_name=$(basename "$service_path")

    # Skip root se è monorepo
    if [ "$service_path" == "." ] && [ "$PROJECT_TYPE" == "monorepo" ]; then
      continue
    fi

    # Rileva tipo servizio analizzando dependencies
    service_type=$(detect_service_type "$pkg")

    services+=("$service_name:$service_path:$service_type")
  done

  # Flutter/Dart projects
  for pubspec in $(find . -name "pubspec.yaml" -not -path "*/.*"); do
    service_path=$(dirname "$pubspec")
    service_name=$(basename "$service_path")
    service_type="mobile"
    services+=("$service_name:$service_path:$service_type")
  done

  # Go projects
  for gomod in $(find . -name "go.mod" -not -path "*/.*"); do
    service_path=$(dirname "$gomod")
    service_name=$(basename "$service_path")
    service_type="backend"
    services+=("$service_name:$service_path:$service_type")
  done

  # Python projects
  for req in $(find . -name "requirements.txt" -not -path "*/.*" -not -path "*/venv/*"); do
    service_path=$(dirname "$req")
    service_name=$(basename "$service_path")
    service_type=$(detect_python_type "$req")
    services+=("$service_name:$service_path:$service_type")
  done

  # Se nessun servizio trovato, usa root come servizio unico
  if [ ${#services[@]} -eq 0 ]; then
    project_name=$(basename "$PWD")
    service_type=$(detect_root_type)
    services+=("$project_name:.:$service_type")
  fi

  echo "${services[@]}"
}

detect_service_type() {
  local package_json="$1"

  # Leggi dependencies
  if grep -q "express\|fastify\|@nestjs/core\|koa" "$package_json"; then
    echo "backend"
  elif grep -q "react\|vue\|angular\|@angular/core\|svelte" "$package_json"; then
    echo "frontend"
  elif grep -q "react-native" "$package_json"; then
    echo "mobile"
  elif grep -q "@types/node" "$package_json" && grep -q "\"bin\"" "$package_json"; then
    echo "cli"
  else
    # Fallback: analizza struttura directory
    service_dir=$(dirname "$package_json")
    if [ -d "$service_dir/src/controllers" ] || [ -d "$service_dir/src/routes" ]; then
      echo "backend"
    elif [ -d "$service_dir/src/components" ] || [ -d "$service_dir/src/pages" ]; then
      echo "frontend"
    else
      echo "api"  # Default generico
    fi
  fi
}

detect_python_type() {
  local req_file="$1"

  if grep -q "fastapi\|flask\|django" "$req_file"; then
    echo "backend"
  else
    echo "api"
  fi
}

detect_root_type() {
  # Rileva tipo progetto dalla root
  if [ -f "package.json" ]; then
    detect_service_type "package.json"
  elif [ -f "pubspec.yaml" ]; then
    echo "mobile"
  elif [ -f "go.mod" ]; then
    echo "backend"
  else
    echo "generic"
  fi
}

# Esegui rilevamento
SERVICES_ARRAY=($(find_services))
```

#### 2.3 Analisi Dettagliata per Servizio

Per ogni servizio rilevato, analizza:

```bash
analyze_service() {
  local service_name="$1"
  local service_path="$2"
  local service_type="$3"

  echo "🔍 Analizzando $service_name ($service_type)..."

  # Rileva linguaggio principale
  primary_lang=$(detect_primary_language "$service_path")

  # Rileva framework
  primary_framework=$(detect_framework "$service_path" "$service_type")

  # Rileva database (se backend)
  has_database="false"
  db_type="none"
  if [ "$service_type" == "backend" ] || [ "$service_type" == "api" ]; then
    db_info=$(detect_database "$service_path")
    if [ "$db_info" != "none" ]; then
      has_database="true"
      db_type="$db_info"
    fi
  fi

  # Rileva testing framework
  test_framework=$(detect_test_framework "$service_path")

  # Conta file e LOC
  file_count=$(find "$service_path" -type f -not -path "*/node_modules/*" -not -path "*/dist/*" | wc -l)
  loc_count=$(find "$service_path" -type f \( -name "*.ts" -o -name "*.js" -o -name "*.py" -o -name "*.dart" -o -name "*.go" \) -not -path "*/node_modules/*" -exec wc -l {} + 2>/dev/null | tail -1 | awk '{print $1}')

  # Crea JSON metadati servizio
  cat > "docs/.toduba-meta/service_${service_name}.json" <<EOF
{
  "name": "$service_name",
  "path": "$service_path",
  "type": "$service_type",
  "primary_language": "$primary_lang",
  "primary_framework": "$primary_framework",
  "has_database": $has_database,
  "database_type": "$db_type",
  "test_framework": "$test_framework",
  "file_count": $file_count,
  "loc_count": $loc_count,
  "analyzed_at": "$(date -u +%Y-%m-%dT%H:%M:%SZ)"
}
EOF

  echo "✅ Analisi completata: $service_name"
}
```

### STEP 3: Creazione Struttura Directory

```bash
echo "📁 Creando struttura documentazione..."

# Crea struttura base
mkdir -p docs/.toduba-meta
mkdir -p docs/global/adr
mkdir -p docs/services
mkdir -p docs/operations

# Per ogni servizio, crea cartella
for service in "${SERVICES_ARRAY[@]}"; do
  IFS=':' read -r name path type <<< "$service"
  mkdir -p "docs/services/$name"
done

echo "✅ Struttura creata"
```

### STEP 4: Generazione Documentazione

#### 4.1 Documentazione Global

```bash
generate_global_docs() {
  echo "📝 Generando documentazione globale..."

  # Global README.md
  generate_from_template \
    "templates/docs/tier1/README.template.md" \
    "docs/global/README.md" \
    "global" \
    ""

  # Global ARCHITECTURE.md
  generate_from_template \
    "templates/docs/tier1/ARCHITECTURE.template.md" \
    "docs/global/ARCHITECTURE.md" \
    "global" \
    ""

  # Global SETUP.md (se monorepo)
  if [ "$PROJECT_TYPE" == "monorepo" ]; then
    generate_from_template \
      "templates/docs/tier1/SETUP.template.md" \
      "docs/global/SETUP.md" \
      "global" \
      ""
  fi

  # CONTRIBUTING.md
  generate_from_template \
    "templates/docs/tier1/CONTRIBUTING.template.md" \
    "docs/global/CONTRIBUTING.md" \
    "global" \
    ""

  # ADR Template e README
  cp "templates/docs/tier1/ADR-TEMPLATE.template.md" "docs/global/adr/0001-template.md"

  cat > "docs/global/adr/README.md" <<'EOF'
# Architecture Decision Records (ADR)

Questo directory contiene le Architecture Decision Records (ADR) del progetto.

## Cosa sono le ADR?

Le ADR documentano decisioni architetturali significative prese durante lo sviluppo del progetto, inclusi il contesto, le alternative considerate e le conseguenze.

## Come creare una nuova ADR

1. Copia il template: `cp 0001-template.md XXXX-your-decision.md`
2. Compila tutte le sezioni
3. Commit e crea PR per review

## Indice ADR

<!-- TODO: Aggiungere ADR quando create -->

EOF

  echo "✅ Documentazione globale generata"
}
```

#### 4.2 Documentazione per Servizio (Tier 1 + Tier 2)

```bash
generate_service_docs() {
  local service_name="$1"
  local service_path="$2"
  local service_type="$3"

  echo "📝 Generando documentazione per: $service_name..."

  # Leggi metadati servizio
  local service_meta="docs/.toduba-meta/service_${service_name}.json"

  # TIER 1: Sempre generato
  generate_from_template \
    "templates/docs/tier1/README.template.md" \
    "docs/services/$service_name/README.md" \
    "service" \
    "$service_meta"

  generate_from_template \
    "templates/docs/tier1/SETUP.template.md" \
    "docs/services/$service_name/SETUP.md" \
    "service" \
    "$service_meta"

  generate_from_template \
    "templates/docs/tier1/ARCHITECTURE.template.md" \
    "docs/services/$service_name/ARCHITECTURE.md" \
    "service" \
    "$service_meta"

  generate_from_template \
    "templates/docs/tier1/TECH-STACK.template.md" \
    "docs/services/$service_name/TECH-STACK.md" \
    "service" \
    "$service_meta"

  generate_from_template \
    "templates/docs/tier1/STYLE-GUIDE.template.md" \
    "docs/services/$service_name/STYLE-GUIDE.md" \
    "service" \
    "$service_meta"

  # TIER 2: Condizionale

  # ENDPOINTS.md - solo se backend o api
  if [ "$service_type" == "backend" ] || [ "$service_type" == "api" ]; then
    generate_from_template \
      "templates/docs/tier2/ENDPOINTS.template.md" \
      "docs/services/$service_name/ENDPOINTS.md" \
      "service" \
      "$service_meta"
  fi

  # DATABASE.md - solo se ha database
  local has_db=$(cat "$service_meta" | grep "has_database" | grep "true")
  if [ -n "$has_db" ]; then
    generate_from_template \
      "templates/docs/tier2/DATABASE.template.md" \
      "docs/services/$service_name/DATABASE.md" \
      "service" \
      "$service_meta"
  fi

  # TESTING.md - sempre per Tier 2
  generate_from_template \
    "templates/docs/tier2/TESTING.template.md" \
    "docs/services/$service_name/TESTING.md" \
    "service" \
    "$service_meta"

  # TROUBLESHOOTING.md - sempre per Tier 2
  generate_from_template \
    "templates/docs/tier2/TROUBLESHOOTING.template.md" \
    "docs/services/$service_name/TROUBLESHOOTING.md" \
    "service" \
    "$service_meta"

  echo "✅ Documentazione $service_name generata"
}
```

#### 4.3 Documentazione Operations

```bash
generate_operations_docs() {
  echo "📝 Generando documentazione operations..."

  # Crea template placeholder per operations docs
  cat > "docs/operations/DEPLOYMENT.md" <<'EOF'
# Deployment Guide

> 🚀 Guida al deployment del progetto
> Ultimo aggiornamento: {{TIMESTAMP}}

## Overview

<!-- TODO: Descrivere strategia di deployment -->

## Environments

### Development
<!-- TODO: Setup environment development -->

### Staging
<!-- TODO: Setup environment staging -->

### Production
<!-- TODO: Setup environment production -->

## Deployment Process

<!-- TODO: Documentare processo deployment -->

## Rollback

<!-- TODO: Procedure di rollback -->

---
*Generato da Toduba System*
EOF

  cat > "docs/operations/CI-CD.md" <<'EOF'
# CI/CD Pipeline

> ⚙️ Documentazione pipeline CI/CD
> Ultimo aggiornamento: {{TIMESTAMP}}

## Pipeline Overview

<!-- TODO: Descrivere pipeline CI/CD -->

## Stages

<!-- TODO: Documentare stages -->

## Configuration

<!-- TODO: File di configurazione -->

---
*Generato da Toduba System*
EOF

  cat > "docs/operations/MONITORING.md" <<'EOF'
# Monitoring & Logging

> 📊 Guida monitoring e logging
> Ultimo aggiornamento: {{TIMESTAMP}}

## Logging Strategy

<!-- TODO: Strategia logging -->

## Monitoring Tools

<!-- TODO: Tool di monitoring -->

## Alerts

<!-- TODO: Configurazione alert -->

---
*Generato da Toduba System*
EOF

  cat > "docs/operations/SECURITY.md" <<'EOF'
# Security Guidelines

> 🛡️ Linee guida sicurezza
> Ultimo aggiornamento: {{TIMESTAMP}}

## Security Best Practices

<!-- TODO: Best practices sicurezza -->

## Authentication & Authorization

<!-- TODO: Auth strategy -->

## Secrets Management

<!-- TODO: Gestione secrets -->

---
*Generato da Toduba System*
EOF

  cat > "docs/operations/ENVIRONMENT-VARS.md" <<'EOF'
# Environment Variables

> ⚙️ Configurazione variabili d'ambiente
> Ultimo aggiornamento: {{TIMESTAMP}}

## Required Variables

<!-- TODO: Variabili richieste -->

## Optional Variables

<!-- TODO: Variabili opzionali -->

## Per Environment

### Development
<!-- TODO: Env development -->

### Production
<!-- TODO: Env production -->

---
*Generato da Toduba System*
EOF

  echo "✅ Documentazione operations generata"
}
```

### STEP 5: Rendering Template con Placeholder

```bash
generate_from_template() {
  local template_file="$1"
  local output_file="$2"
  local scope="$3"         # "global" o "service"
  local metadata_file="$4"  # Path to service metadata JSON (se service)

  # Leggi template
  local content=$(cat "$template_file")

  # Replace placeholder comuni
  content="${content//\{\{TIMESTAMP\}\}/$(date -u +%Y-%m-%dT%H:%M:%SZ)}"
  content="${content//\{\{TODUBA_VERSION\}\}/2.0.0}"

  if [ "$scope" == "global" ]; then
    # Placeholder globali
    local project_name=$(basename "$PWD")
    content="${content//\{\{PROJECT_NAME\}\}/$project_name}"
    content="${content//\{\{PROJECT_DESCRIPTION\}\}/<!-- TODO: Aggiungere descrizione progetto -->}"

  elif [ "$scope" == "service" ] && [ -f "$metadata_file" ]; then
    # Placeholder servizio (da metadata JSON)
    local service_name=$(cat "$metadata_file" | grep -o '"name": *"[^"]*"' | cut -d'"' -f4)
    local service_type=$(cat "$metadata_file" | grep -o '"type": *"[^"]*"' | cut -d'"' -f4)
    local primary_lang=$(cat "$metadata_file" | grep -o '"primary_language": *"[^"]*"' | cut -d'"' -f4)
    local primary_framework=$(cat "$metadata_file" | grep -o '"primary_framework": *"[^"]*"' | cut -d'"' -f4)
    local file_count=$(cat "$metadata_file" | grep -o '"file_count": *[0-9]*' | awk '{print $2}')
    local loc_count=$(cat "$metadata_file" | grep -o '"loc_count": *[0-9]*' | awk '{print $2}')

    content="${content//\{\{SERVICE_NAME\}\}/$service_name}"
    content="${content//\{\{PROJECT_NAME\}\}/$service_name}"
    content="${content//\{\{SERVICE_TYPE\}\}/$service_type}"
    content="${content//\{\{PRIMARY_LANGUAGE\}\}/$primary_lang}"
    content="${content//\{\{PRIMARY_FRAMEWORK\}\}/$primary_framework}"
    content="${content//\{\{TOTAL_FILES\}\}/$file_count}"
    content="${content//\{\{LINES_OF_CODE\}\}/$loc_count}"

    # Placeholder generici (TODO)
    content="${content//\{\{[^}]*\}\}/<!-- TODO: Completare manualmente -->}"
  fi

  # Scrivi output
  echo "$content" > "$output_file"
}
```

### STEP 6: Creazione Metadata

```bash
create_metadata() {
  echo "💾 Creando metadata..."

  # project-type.json
  cat > "docs/.toduba-meta/project-type.json" <<EOF
{
  "type": "$PROJECT_TYPE",
  "detected_at": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "root_path": "$(pwd)",
  "services_count": ${#SERVICES_ARRAY[@]}
}
EOF

  # services.json
  echo "{" > "docs/.toduba-meta/services.json"
  echo '  "services": [' >> "docs/.toduba-meta/services.json"

  local first=true
  for service in "${SERVICES_ARRAY[@]}"; do
    IFS=':' read -r name path type <<< "$service"

    if [ "$first" = true ]; then
      first=false
    else
      echo "," >> "docs/.toduba-meta/services.json"
    fi

    echo "    {" >> "docs/.toduba-meta/services.json"
    echo "      \"name\": \"$name\"," >> "docs/.toduba-meta/services.json"
    echo "      \"path\": \"$path\"," >> "docs/.toduba-meta/services.json"
    echo "      \"type\": \"$type\"" >> "docs/.toduba-meta/services.json"
    echo -n "    }" >> "docs/.toduba-meta/services.json"
  done

  echo "" >> "docs/.toduba-meta/services.json"
  echo "  ]" >> "docs/.toduba-meta/services.json"
  echo "}" >> "docs/.toduba-meta/services.json"

  # last-update.json
  cat > "docs/.toduba-meta/last-update.json" <<EOF
{
  "timestamp": "$(date -u +%Y-%m-%dT%H:%M:%SZ)",
  "git_commit": "$(git rev-parse HEAD 2>/dev/null || echo 'unknown')",
  "git_branch": "$(git rev-parse --abbrev-ref HEAD 2>/dev/null || echo 'unknown')",
  "toduba_version": "2.0.0",
  "full_generation": true
}
EOF

  echo "✅ Metadata creati"
}
```

## 📊 Output Finale

```bash
echo ""
echo "✅ =========================================="
echo "✅  Toduba Init V2.0 - Completato!"
echo "✅ =========================================="
echo ""
echo "📊 Riepilogo:"
echo "   • Tipo progetto: $PROJECT_TYPE"
echo "   • Servizi rilevati: ${#SERVICES_ARRAY[@]}"
for service in "${SERVICES_ARRAY[@]}"; do
  IFS=':' read -r name path type <<< "$service"
  echo "     - $name ($type)"
done
echo ""
echo "📁 Documentazione generata in: ./docs/"
echo ""
echo "📂 Struttura creata:"
echo "   ├── global/         (Documentazione globale)"
echo "   ├── services/       (Documentazione per servizio)"
echo "   ├── operations/     (DevOps e operations)"
echo "   └── .toduba-meta/   (Metadata e cache)"
echo ""
echo "📝 Prossimi passi:"
echo "   1. ✏️  Completa i placeholder TODO nei file generati"
echo "   2. 📖 Revisiona la documentazione"
echo "   3. 🔄 Usa /toduba-update-docs per aggiornamenti futuri"
echo "   4. 💾 Committa la cartella docs/ nel repository"
echo ""
echo "💡 Tips:"
echo "   • I template sono semi-dinamici con placeholder intelligenti"
echo "   • Sezioni con TODO vanno completate manualmente"
echo "   • La struttura è ottimizzata per monorepo e single service"
echo "   • Usa /toduba-update-docs per update incrementali (molto più veloce)"
echo ""
```

## 🎯 Note Implementazione

1. **Auto-detection robusto**: Rileva automaticamente tipo progetto e servizi
2. **Template semi-dinamici**: Placeholder popolati da analisi + TODO per completamento manuale
3. **Struttura sempre consistente**: `docs/services/` sempre presente anche con 1 solo servizio
4. **Tier 1 + Tier 2**: Tier 1 sempre generato, Tier 2 condizionale (ENDPOINTS solo backend, DATABASE solo se ha DB)
5. **Metadata tracking**: `.toduba-meta/` traccia tutto per update incrementali futuri
6. **Fallback intelligente**: Se detection fallisce, usa default ragionevoli

## 🚨 Gestione Errori

- **Directory non scrivibile**: Alert utente
- **Template mancanti**: Usa fallback generico
- **Detection fallita**: Usa progetto root come singolo servizio generico
- **Git non inizializzato**: Procedi senza info git (ok)

## ⚡ Performance

- Target: < 10 secondi per init completo
- Parallel processing dove possibile
- Cache metadata per future operazioni

---

*Toduba Init V2.0 - Smart Documentation Generator*
