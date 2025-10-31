# Configurazione Claude Desktop - Toduba Plugin System

Guida completa alla configurazione di Claude Desktop per il Toduba Plugin System.

## 🚀 Quick Start

### 1. Installazione Plugin

```bash
# Metodo 1: Marketplace (consigliato)
/plugin marketplace add https://github.com/tiboxtibo/custom-claude-plugins

# Metodo 2: Clone + Symlink
git clone https://github.com/tiboxtibo/custom-claude-plugins.git
cd custom-claude-plugins
ln -s $(pwd) ~/.claude/plugins/custom-claude-plugins

# Metodo 3: Direct Copy
git clone https://github.com/tiboxtibo/custom-claude-plugins.git
cp -r custom-claude-plugins ~/.claude/plugins/
```

### 2. Riavvia Claude Desktop

Dopo l'installazione, riavvia Claude Desktop per caricare il plugin.

### 3. Verifica Installazione

```bash
# In Claude Desktop, verifica che i comandi siano disponibili
/toduba-help

# Verifica agenti disponibili
/toduba-help orchestrator
```

---

## 📁 Struttura Directory Claude Desktop

```
~/.claude/
├── settings.json           # Configurazione globale (MCP servers)
├── settings.local.json     # Override locali (gitignored)
└── plugins/
    └── custom-claude-plugins/   # Toduba plugin
        ├── agents/              # 8 agenti
        ├── commands/            # 10 comandi
        ├── templates/           # Template system
        ├── config.template.json # Template configurazione
        └── README.md
```

---

## ⚙️ Configurazione MCP Servers

Il Toduba Plugin System utilizza MCP (Model Context Protocol) servers per funzionalità avanzate.

### File: ~/.claude/settings.json

```json
{
  "mcpServers": {
    "playwright": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@playwright/mcp-server"]
    },
    "memory": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"]
    },
    "MongoDB": {
      "type": "http",
      "url": "https://your-mongodb-mcp.example.com/mcp/",
      "headers": {
        "Authorization": "Bearer YOUR_MONGODB_API_TOKEN"
      }
    }
  }
}
```

### MCP Servers Supportati

#### 1. Playwright (Frontend Testing)

**Utilizzo**: Browser automation per frontend testing

**Installazione**:
```bash
# Viene installato automaticamente al primo uso
npx -y @playwright/mcp-server
```

**Agenti che lo usano**:
- `toduba-frontend-engineer`
- `toduba-qa-engineer`

**Tools disponibili**:
- `browser_navigate`, `browser_snapshot`
- `browser_click`, `browser_type`
- `browser_take_screenshot`
- `browser_evaluate`
- `browser_wait_for`

#### 2. Memory (Caching & Persistence)

**Utilizzo**: Cache persistente per performance optimization

**Installazione**:
```bash
npm install -g @modelcontextprotocol/server-memory
```

**Agenti che lo usano**:
- Tutti gli agenti (dove applicabile)

**Tools disponibili**:
- `create_entities`, `create_relations`
- `read_graph`, `search_nodes`

**Benefits**:
- Cache hit rate: 78%
- Update time: 93% faster
- Memory footprint: -60%

#### 3. MongoDB (Custom - Opzionale)

**Utilizzo**: Database operations per backend development

**Setup**:
1. Deploy tuo MCP server MongoDB
2. Ottieni API token
3. Configura in `settings.json`

**Agenti che lo usano**:
- `toduba-backend-engineer`

**Tools disponibili**:
- `list-databases`, `list-collections`
- `collection-schema`
- `find`, `aggregate`

---

## 🛠️ Configurazione Plugin Personalizzata

### File: config.template.json

Template per personalizzare il comportamento del plugin.

```json
{
  "workspace": {
    "rootDirectory": "workspace",
    "subdirectories": {
      "workPackages": "work_packages",
      "journals": "journals",
      "validationResults": "validation_results"
    }
  },

  "agents": {
    "orchestrator": {
      "name": "Atlas",
      "neverImplements": true,
      "alwaysCoordinates": true
    },
    "developmentAgents": [
      "ai-engineer",
      "backend-engineer",
      "frontend-engineer"
    ],
    "validationAgents": [
      "qa-engineer",
      "code-reviewer"
    ]
  },

  "features": {
    "enableJournaling": true,
    "enableWorkPackages": true,
    "enableCompletionFlags": true
  },

  "paths": {
    "workPackage": "{{workspaceRoot}}/work_packages/{{role}}/{{taskId}}_{{role}}_workpackage.md",
    "journal": "{{workspaceRoot}}/journals/{{role}}/{{taskId}}_{{agentName}}_journal.md"
  }
}
```

### Personalizzazione Workspace

```json
{
  "workspace": {
    "rootDirectory": "my-custom-workspace",
    "subdirectories": {
      "workPackages": "tasks",
      "journals": "logs",
      "validationResults": "qa-reports"
    }
  }
}
```

Questo cambierà la struttura a:
```
my-custom-workspace/
├── tasks/
├── logs/
└── qa-reports/
```

---

## 🎨 Personalizzazione Agenti

### Colori

Ogni agente ha un colore associato visibile in Claude Desktop:

| Agente | Colore | RGB |
|--------|--------|-----|
| orchestrator | Purple | #9333EA |
| backend-engineer | Blue | #2563EB |
| frontend-engineer | Green | #16A34A |
| mobile-engineer | Orange | #EA580C |
| qa-engineer | Red | #DC2626 |
| test-engineer | Yellow | #CA8A04 |
| codebase-analyzer | Cyan | #0891B2 |
| documentation-generator | Pink | #DB2777 |

### Naming

Puoi personalizzare i nomi degli agenti nel file agente `.md`:

```yaml
---
name: my-custom-backend-engineer  # Cambia qui
description: Your custom description
tools:
  - Read
  - Write
color: blue
---
```

---

## 📂 Configurazione Progetto Locale

### File: .claude/settings.local.json

Per override specifici del progetto (gitignored):

```json
{
  "toduba": {
    "workspaceRoot": "./toduba-workspace",
    "autoUpdateDocs": true,
    "autoUpdateThreshold": 10,
    "defaultUltraThinkMode": "standard",
    "enablePreCommitHooks": true
  }
}
```

### Opzioni Disponibili

#### `workspaceRoot`
- **Type**: string
- **Default**: "workspace"
- **Descrizione**: Directory root per work artifacts

#### `autoUpdateDocs`
- **Type**: boolean
- **Default**: true
- **Descrizione**: Auto-aggiorna docs quando > threshold file modificati

#### `autoUpdateThreshold`
- **Type**: number
- **Default**: 10
- **Descrizione**: Numero file modificati che triggera auto-update docs

#### `defaultUltraThinkMode`
- **Type**: "quick" | "standard" | "deep"
- **Default**: "standard"
- **Descrizione**: Modalità default per ultra-think analysis

#### `enablePreCommitHooks`
- **Type**: boolean
- **Default**: true
- **Descrizione**: Abilita pre-commit hooks Toduba

---

## 🔒 Configurazione Sicurezza

### .gitignore

Assicurati che il tuo `.gitignore` includa:

```gitignore
# Claude settings con secrets
.claude/settings.local.json

# Workspace con dati sensibili
workspace/
toduba-workspace/

# Environment files
.env
.env.local
.env.*.local

# Snapshots (possono essere grandi)
.toduba/snapshots/
```

### Secrets Management

**MAI** committare:
- API tokens nei file di configurazione
- Database credentials
- Private keys
- Access tokens

**Usa invece**:
- Environment variables
- `settings.local.json` (gitignored)
- Secret management tools (1Password, Vault)

**Esempio sicuro**:
```json
{
  "mcpServers": {
    "MongoDB": {
      "type": "http",
      "url": "${MONGODB_MCP_URL}",
      "headers": {
        "Authorization": "Bearer ${MONGODB_API_TOKEN}"
      }
    }
  }
}
```

E in `.env`:
```bash
MONGODB_MCP_URL=https://your-mongodb-mcp.example.com/mcp/
MONGODB_API_TOKEN=your_secret_token_here
```

---

## 🔄 Aggiornamento Plugin

### Auto-Update (Marketplace)

```bash
/plugin marketplace update custom-claude-plugins
```

### Manual Update (Git)

```bash
cd ~/.claude/plugins/custom-claude-plugins
git pull origin main
```

### Verifica Versione

```bash
/toduba-help

# Output include versione:
# Toduba Plugin System v2.0
```

---

## 🧪 Testing Configurazione

### Test MCP Servers

**Playwright**:
```bash
# In Claude Desktop
Use frontend-engineer agent to navigate to https://example.com
```

**Memory**:
```bash
# Verifica cache
Use backend-engineer to check memory cache statistics
```

**MongoDB**:
```bash
# List databases
Use backend-engineer to list MongoDB databases
```

### Test Agenti

```bash
# Test orchestrator
/toduba-ultra-think "simple test task"

# Test backend engineer
Use backend-engineer to analyze src/api structure

# Test frontend engineer
Use frontend-engineer to analyze src/components structure
```

### Test Comandi

```bash
# Test init
/toduba-init --verbose

# Test help
/toduba-help

# Test template
/toduba-template --list
```

---

## 🐛 Troubleshooting

### Plugin non caricato

**Sintomi**: Comandi `/toduba-*` non disponibili

**Soluzioni**:
1. Verifica path: `~/.claude/plugins/custom-claude-plugins`
2. Verifica struttura file (agents/, commands/)
3. Riavvia Claude Desktop
4. Check logs: `~/.claude/logs/`

### MCP Server non risponde

**Sintomi**: Errori quando usi agenti che richiedono MCP

**Soluzioni**:
1. Verifica configurazione in `settings.json`
2. Test connection manualmente:
   ```bash
   npx -y @playwright/mcp-server
   ```
3. Check API tokens/credentials
4. Verifica network/firewall

### Agente non trovato

**Sintomi**: "Agent not found" quando usi Task tool

**Soluzioni**:
1. Verifica nome agente (case-sensitive)
2. Check file `.md` in `agents/` directory
3. Verifica YAML frontmatter nel file agente
4. Restart Claude Desktop

### Performance Issues

**Sintomi**: Agenti lenti, timeout

**Soluzioni**:
1. Abilita memory cache:
   ```json
   {"mcpServers": {"memory": {...}}}
   ```
2. Usa `--smart` flag per docs updates
3. Riduci workspace size (cleanup old files)
4. Check disk space

---

## 📊 Monitoring & Logs

### Claude Desktop Logs

```bash
# macOS
tail -f ~/.claude/logs/main.log

# Windows
type %APPDATA%\.claude\logs\main.log

# Linux
tail -f ~/.claude/logs/main.log
```

### Toduba Workspace Logs

```
workspace/
└── journals/
    ├── backend-engineer/
    │   └── task_001_backend_journal.md
    └── frontend-engineer/
        └── task_002_frontend_journal.md
```

### Metrics Tracking

Il sistema traccia automaticamente:
- Tempo esecuzione agenti
- Cache hit rate
- File modificati per task
- Test coverage trends

Accessibili via:
```bash
# Nel workspace
cat workspace/metrics.json
```

---

## 🔧 Advanced Configuration

### Custom MCP Server

Se hai un MCP server personalizzato:

```json
{
  "mcpServers": {
    "my-custom-server": {
      "type": "http",
      "url": "https://my-server.example.com/mcp/",
      "headers": {
        "Authorization": "Bearer ${MY_API_TOKEN}"
      }
    }
  }
}
```

Poi modifica agente per usarlo:

```yaml
---
name: my-custom-agent
tools:
  - Read
  - Write
  - mcp__my-custom-server__custom_tool_1
  - mcp__my-custom-server__custom_tool_2
---
```

### Custom Commands

Crea comandi personalizzati in `commands/`:

```
commands/
└── my-custom-command.md
```

**my-custom-command.md**:
```markdown
---
allowed-tools:
  - Read
  - Write
argument-hint: "[--option]"
description: "Your custom command description"
---

# My Custom Command

Your command implementation...
```

Sarà disponibile come `/my-custom-command`

### Custom Templates

Aggiungi template in `templates/`:

```
templates/
└── my-template/
    ├── template.json
    └── files/
        └── component.ts.template
```

Uso:
```bash
/toduba-template my-template --variables name=MyComponent
```

---

## 📚 Best Practices

### 1. Workspace Organization

✅ **DO**:
- Keep workspace in project root
- Use descriptive work package names
- Clean up completed work packages periodically

❌ **DON'T**:
- Commit workspace to git (add to .gitignore)
- Mix multiple projects in same workspace
- Keep old snapshots indefinitely

### 2. MCP Configuration

✅ **DO**:
- Use environment variables for secrets
- Test MCP connections before use
- Keep settings.json backed up

❌ **DON'T**:
- Hardcode API tokens
- Share settings.json publicly
- Use unencrypted HTTP for MCP

### 3. Agent Usage

✅ **DO**:
- Use orchestrator for coordination
- Let agenti work in parallel when possible
- Validate with QA engineer

❌ **DON'T**:
- Call development agents directly for complex tasks
- Skip test validation
- Bypass orchestrator workflow

---

## 🆘 Support

### Documentazione

- **Plugin README**: `~/.claude/plugins/custom-claude-plugins/README.md`
- **Generated Docs**: `./docs/` (dopo `/toduba-init`)
- **In-app Help**: `/toduba-help`

### Community

- **GitHub Issues**: [custom-claude-plugins/issues](https://github.com/tiboxtibo/custom-claude-plugins/issues)
- **Discussions**: [GitHub Discussions](https://github.com/tiboxtibo/custom-claude-plugins/discussions)

### Report Bug

```bash
# Raccogli info per bug report
/toduba-help --verbose > debug-info.txt
```

Include:
- Claude Desktop version
- Plugin version
- OS version
- Error messages/logs
- Steps to reproduce

---

**Documento generato da**: Toduba Documentation Generator
**Versione**: 2.0.0
**Data**: 2025-10-31
