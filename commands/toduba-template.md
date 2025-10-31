---
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Task
argument-hint: "[template-name] [--list] [--variables key=value]"
description: "Sistema di template per workflow comuni e scaffolding rapido"
model: haiku
---

# Toduba Template - Template Workflows System 📝

## Obiettivo
Fornire template predefiniti per workflow comuni, permettendo scaffolding rapido e consistente di componenti, API, e applicazioni complete.

## Argomenti
- `[template-name]`: Nome del template da usare
- `--list`: Lista tutti i template disponibili
- `--variables`: Variabili per il template (key=value)
- `--preview`: Mostra preview senza creare file
- `--customize`: Modalità interattiva per personalizzazione

Argomenti ricevuti: $ARGUMENTS

## Template System Architecture

```typescript
interface Template {
  name: string;
  description: string;
  category: 'api' | 'component' | 'app' | 'test' | 'config';
  variables: Variable[];
  files: FileTemplate[];
  hooks?: {
    preGenerate?: string;
    postGenerate?: string;
  };
}

interface Variable {
  name: string;
  description: string;
  type: 'string' | 'boolean' | 'select';
  default?: any;
  required: boolean;
  options?: string[];
}

interface FileTemplate {
  path: string;
  template: string;
  condition?: string;
}
```

## Available Templates Gallery

```
📚 TODUBA TEMPLATE GALLERY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🔷 API TEMPLATES
─────────────────
crud-api           Complete CRUD API with validation
rest-endpoint      Single REST endpoint
graphql-resolver   GraphQL resolver with types
websocket-server   WebSocket server setup
microservice       Microservice with Docker

🔷 COMPONENT TEMPLATES
──────────────────────
react-component    React component with tests
vue-component      Vue 3 composition component
angular-component  Angular component with service
flutter-widget     Flutter stateful widget
web-component      Native web component

🔷 APPLICATION TEMPLATES
────────────────────────
fullstack-app      Complete full-stack application
mobile-app         Flutter mobile app
electron-app       Electron desktop app
cli-tool           CLI application
chrome-extension   Chrome extension

🔷 TESTING TEMPLATES
────────────────────
unit-test          Unit test suite
integration-test   Integration test setup
e2e-test          E2E test with Playwright
performance-test   Performance test suite

🔷 CONFIGURATION TEMPLATES
───────────────────────────
docker-setup       Docker + docker-compose
ci-cd-pipeline     GitHub Actions/GitLab CI
kubernetes         K8s deployment configs
nginx-config       Nginx configuration
env-setup         Environment setup
```

## Template Usage Flow

### Step 1: Select Template
```bash
/toduba-template crud-api --variables resource=product
```

### Step 2: Variable Input
```
🎯 Template: crud-api
━━━━━━━━━━━━━━━━━━━━━━

Required variables:
✓ resource: product
? database: [postgres/mysql/mongodb]: postgres
? authentication: [jwt/session/oauth]: jwt
? validation: [joi/yup/zod]: joi

Optional variables:
? includeTests: [Y/n]: Y
? includeDocker: [y/N]: n
? includeSwagger: [Y/n]: Y
```

### Step 3: Preview Generation
```
📋 Files to be created:
━━━━━━━━━━━━━━━━━━━━━━━
✓ api/products/controller.js
✓ api/products/model.js
✓ api/products/routes.js
✓ api/products/validation.js
✓ api/products/service.js
✓ tests/products.test.js
✓ docs/products-api.yaml

Total: 7 files
Continue? [Y/n]:
```

## Template Definitions

### CRUD API Template
```yaml
name: crud-api
description: Complete CRUD API with all operations
category: api
variables:
  - name: resource
    type: string
    required: true
    description: Resource name (singular)

  - name: database
    type: select
    options: [postgres, mysql, mongodb]
    default: postgres
    required: true

  - name: authentication
    type: select
    options: [jwt, session, oauth, none]
    default: jwt

  - name: validation
    type: select
    options: [joi, yup, zod, ajv]
    default: joi

  - name: includeTests
    type: boolean
    default: true

files:
  - path: api/{{resource}}s/controller.js
    template: |
      const { {{Resource}}Service } = require('./service');
      const { validate{{Resource}} } = require('./validation');

      class {{Resource}}Controller {
        async create(req, res, next) {
          try {
            const validated = await validate{{Resource}}(req.body);
            const result = await {{Resource}}Service.create(validated);
            res.status(201).json({
              success: true,
              data: result
            });
          } catch (error) {
            next(error);
          }
        }

        async getAll(req, res, next) {
          try {
            const { page = 1, limit = 10, ...filters } = req.query;
            const result = await {{Resource}}Service.findAll({
              page: Number(page),
              limit: Number(limit),
              filters
            });
            res.json({
              success: true,
              data: result.data,
              pagination: result.pagination
            });
          } catch (error) {
            next(error);
          }
        }

        async getById(req, res, next) {
          try {
            const result = await {{Resource}}Service.findById(req.params.id);
            if (!result) {
              return res.status(404).json({
                success: false,
                message: '{{Resource}} not found'
              });
            }
            res.json({
              success: true,
              data: result
            });
          } catch (error) {
            next(error);
          }
        }

        async update(req, res, next) {
          try {
            const validated = await validate{{Resource}}(req.body, true);
            const result = await {{Resource}}Service.update(req.params.id, validated);
            res.json({
              success: true,
              data: result
            });
          } catch (error) {
            next(error);
          }
        }

        async delete(req, res, next) {
          try {
            await {{Resource}}Service.delete(req.params.id);
            res.status(204).send();
          } catch (error) {
            next(error);
          }
        }
      }

      module.exports = new {{Resource}}Controller();

  - path: api/{{resource}}s/routes.js
    template: |
      const router = require('express').Router();
      const controller = require('./controller');
      {{#if authentication}}
      const { authenticate } = require('../../middleware/auth');
      {{/if}}

      router.post('/',
        {{#if authentication}}authenticate,{{/if}}
        controller.create
      );

      router.get('/',
        {{#if authentication}}authenticate,{{/if}}
        controller.getAll
      );

      router.get('/:id',
        {{#if authentication}}authenticate,{{/if}}
        controller.getById
      );

      router.put('/:id',
        {{#if authentication}}authenticate,{{/if}}
        controller.update
      );

      router.delete('/:id',
        {{#if authentication}}authenticate,{{/if}}
        controller.delete
      );

      module.exports = router;
```

### React Component Template
```yaml
name: react-component
description: React functional component with hooks
category: component
variables:
  - name: componentName
    type: string
    required: true

  - name: hasState
    type: boolean
    default: true

  - name: hasProps
    type: boolean
    default: true

  - name: style
    type: select
    options: [css, scss, styled-components, tailwind]
    default: css

files:
  - path: components/{{componentName}}/{{componentName}}.tsx
    template: |
      import React{{#if hasState}}, { useState, useEffect }{{/if}} from 'react';
      {{#if style === 'styled-components'}}
      import styled from 'styled-components';
      {{else}}
      import './{{componentName}}.{{style}}';
      {{/if}}

      {{#if hasProps}}
      interface {{componentName}}Props {
        title?: string;
        children?: React.ReactNode;
        onClick?: () => void;
      }
      {{/if}}

      export const {{componentName}}: React.FC{{#if hasProps}}<{{componentName}}Props>{{/if}}> = ({
        {{#if hasProps}}
        title = 'Default Title',
        children,
        onClick
        {{/if}}
      }) => {
        {{#if hasState}}
        const [isLoading, setIsLoading] = useState(false);
        const [data, setData] = useState<any>(null);

        useEffect(() => {
          // Component mount logic
          return () => {
            // Cleanup
          };
        }, []);
        {{/if}}

        return (
          <div className="{{kebabCase componentName}}">
            {{#if hasProps}}
            <h2>{title}</h2>
            {{/if}}
            {{#if hasState}}
            {isLoading ? (
              <div>Loading...</div>
            ) : (
              <div>{children}</div>
            )}
            {{else}}
            {children}
            {{/if}}
            {{#if hasProps}}
            <button onClick={onClick}>Click me</button>
            {{/if}}
          </div>
        );
      };

  - path: components/{{componentName}}/{{componentName}}.test.tsx
    condition: includeTests
    template: |
      import { render, screen, fireEvent } from '@testing-library/react';
      import { {{componentName}} } from './{{componentName}}';

      describe('{{componentName}}', () => {
        it('renders without crashing', () => {
          render(<{{componentName}} />);
        });

        {{#if hasProps}}
        it('displays the title', () => {
          render(<{{componentName}} title="Test Title" />);
          expect(screen.getByText('Test Title')).toBeInTheDocument();
        });

        it('calls onClick handler', () => {
          const handleClick = jest.fn();
          render(<{{componentName}} onClick={handleClick} />);
          fireEvent.click(screen.getByText('Click me'));
          expect(handleClick).toHaveBeenCalled();
        });
        {{/if}}
      });
```

### Full-Stack App Template
```yaml
name: fullstack-app
description: Complete full-stack application setup
category: app
variables:
  - name: appName
    type: string
    required: true

  - name: frontend
    type: select
    options: [react, vue, angular, nextjs]
    default: react

  - name: backend
    type: select
    options: [express, fastify, nestjs, fastapi]
    default: express

  - name: database
    type: select
    options: [postgres, mysql, mongodb, sqlite]
    default: postgres

files:
  # Project structure
  - path: .gitignore
    template: |
      node_modules/
      .env
      .env.local
      dist/
      build/
      .DS_Store
      *.log
      .vscode/
      .idea/

  - path: README.md
    template: |
      # {{appName}}

      Full-stack application built with {{frontend}} and {{backend}}.

      ## Quick Start

      \`\`\`bash
      # Install dependencies
      npm install

      # Setup database
      npm run db:setup

      # Start development
      npm run dev
      \`\`\`

      ## Architecture

      - Frontend: {{frontend}}
      - Backend: {{backend}}
      - Database: {{database}}

  - path: docker-compose.yml
    template: |
      version: '3.8'
      services:
        backend:
          build: ./backend
          ports:
            - "3001:3001"
          environment:
            - DATABASE_URL={{database}}://user:pass@db:5432/{{appName}}
          depends_on:
            - db

        frontend:
          build: ./frontend
          ports:
            - "3000:3000"
          depends_on:
            - backend

        db:
          image: {{database}}:latest
          environment:
            {{#if database === 'postgres'}}
            - POSTGRES_USER=user
            - POSTGRES_PASSWORD=pass
            - POSTGRES_DB={{appName}}
            {{/if}}
          volumes:
            - db_data:/var/lib/postgresql/data

      volumes:
        db_data:
```

## Template Engine

```javascript
class TemplateEngine {
  async generate(templateName, variables) {
    const template = await this.loadTemplate(templateName);

    // Validate required variables
    this.validateVariables(template, variables);

    // Process each file template
    const files = [];
    for (const fileTemplate of template.files) {
      if (this.shouldGenerate(fileTemplate, variables)) {
        const path = this.processPath(fileTemplate.path, variables);
        const content = this.processTemplate(fileTemplate.template, variables);

        files.push({ path, content });
      }
    }

    return files;
  }

  processTemplate(template, variables) {
    // Replace {{variable}} with values
    // Handle conditionals {{#if condition}}...{{/if}}
    // Handle loops {{#each array}}...{{/each}}
    // Transform cases: {{pascalCase var}}, {{kebabCase var}}

    return processedContent;
  }
}
```

## Interactive Customization Mode

```
🎨 TEMPLATE CUSTOMIZATION MODE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Template: react-component

[✓] Include TypeScript
[✓] Include tests
[ ] Include Storybook stories
[✓] Include PropTypes
[ ] Include Redux connection
[✓] Include hooks
[ ] Include error boundary

Style system:
○ Plain CSS
● Tailwind CSS
○ Styled Components
○ CSS Modules

State management:
○ None
● useState/useReducer
○ Redux
○ MobX
○ Zustand

File structure:
○ Single file
● Component folder
○ Feature folder

[Generate] [Preview] [Cancel]
```

## Template Hooks

```javascript
hooks: {
  preGenerate: async (variables) => {
    // Validate environment
    // Check dependencies
    // Create directories

    console.log('🔍 Pre-generation checks...');

    if (!fs.existsSync('package.json')) {
      console.log('⚠️ No package.json found');
      const init = await prompt('Initialize npm project? (y/n)');
      if (init === 'y') {
        execSync('npm init -y');
      }
    }
  },

  postGenerate: async (files, variables) => {
    // Install dependencies
    // Run formatters
    // Update indexes

    console.log('📦 Installing dependencies...');

    if (variables.frontend === 'react') {
      execSync('npm install react react-dom');
    }

    console.log('🎨 Formatting files...');
    execSync('npx prettier --write .');

    console.log('✅ Template generated successfully!');
  }
}
```

## Success Output

```
✅ Template Generation Complete!

📊 Summary:
━━━━━━━━━━━━━━━━━━━━━━━━
Template: crud-api
Resource: product
Database: postgres

📁 Files Created (7):
✓ api/products/controller.js
✓ api/products/model.js
✓ api/products/routes.js
✓ api/products/validation.js
✓ api/products/service.js
✓ tests/products.test.js
✓ docs/products-api.yaml

📦 Dependencies Added:
+ express@4.18.2
+ pg@8.11.3
+ joi@17.9.2

🚀 Next Steps:
1. Review generated files
2. Update .env with database credentials
3. Run migrations: npm run db:migrate
4. Start server: npm run dev

💡 Tips:
• Customize validation in validation.js
• Add custom business logic in service.js
• Extend tests in products.test.js
```