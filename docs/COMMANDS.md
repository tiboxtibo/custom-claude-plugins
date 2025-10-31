# Toduba Commands - Reference Completa

Sistema di 10 comandi slash per workflow avanzati di sviluppo.

## 📋 Panoramica Comandi

| Comando | Descrizione | Argomenti | Tools |
|---------|-------------|-----------|-------|
| `/toduba-init` | Genera docs iniziale | --force, --verbose | Read, Write, Bash, Glob, Grep |
| `/toduba-update-docs` | Update incrementale | --check, --full, --smart, --format | Read, Write, Edit, Bash, Glob, Grep |
| `/toduba-commit` | Commit strutturato | [message] | Read, Bash, Grep |
| `/toduba-code-review` | Code review | [file-or-directory], --strict | Read, Glob, Grep, Task |
| `/toduba-ultra-think` | Analisi profonda | [problem] | Task |
| `/toduba-test` | Test suite | --watch, --coverage, --only, --fail-fast | Bash, Read, Grep, Glob, Task |
| `/toduba-rollback` | Rollback system | --last, --steps, --to, --dry-run | Bash, Read, Write, Grep |
| `/toduba-help` | Help system | [command\|agent], --examples, --verbose | Read, Glob, Grep |
| `/toduba-interactive` | Interactive mode | --step-by-step, --auto-pause | Read, Write, Bash, Task |
| `/toduba-template` | Template system | [template-name], --list, --variables | Read, Write, Bash, Glob |

---

## 1. /toduba-init 📚

### Descrizione
Analizza l'intero progetto e genera documentazione completa nella cartella `/docs`.

### Sintassi
```bash
/toduba-init [--force] [--verbose]
```

### Argomenti
- `--force`: Rigenera completamente anche se docs esiste
- `--verbose`: Output dettagliato durante generazione

### Processo

#### Fase 1: Verifica Stato
```bash
# Controlla se /docs esiste
if [ -d "docs" ]; then
  # Legge metadata.json
  # Se --force, cancella e ricomincia
  # Altrimenti, modalità incrementale
fi
```

#### Fase 2: Analisi Progetto
1. **Identifica tipo progetto**
   - package.json → Node.js
   - pubspec.yaml → Flutter
   - requirements.txt → Python
   - Cargo.toml → Rust

2. **Analizza struttura**
   - Directory tree
   - File count per tipo
   - Componenti principali

3. **Analizza componenti**
   - Frontend framework
   - Backend framework
   - Database type
   - Testing setup

#### Fase 3: Generazione Docs

**File generati**:
```
docs/
├── INDEX.md              # Overview progetto
├── ARCHITECTURE.md       # Architettura sistema
├── API_ENDPOINTS.md      # API documentation
├── DATABASE_SCHEMA.md    # Schema database
├── COMPONENTS.md         # UI components
├── MOBILE_STRUCTURE.md   # Mobile app structure
├── DEPENDENCIES.md       # Dipendenze
├── CONFIGURATION.md      # Configurazioni
├── TESTING.md           # Test strategy
├── DEPLOYMENT.md        # Deployment guide
└── metadata.json        # Generation metadata
```

#### Fase 4: Metadata

```json
{
  "version": "1.0.0",
  "generated": "2025-10-31T10:00:00Z",
  "last_updated": "2025-10-31T10:00:00Z",
  "toduba_version": "2.0.0",
  "project_info": {
    "name": "my-project",
    "type": "full-stack",
    "main_language": "TypeScript",
    "frameworks": ["React", "Express"]
  },
  "analysis": {
    "files_analyzed": 245,
    "directories_scanned": 42,
    "time_taken_seconds": 12
  },
  "coverage": {
    "architecture": true,
    "api": true,
    "database": true,
    "components": true
  },
  "git_info": {
    "last_commit": "abc123",
    "branch": "main"
  }
}
```

### Output Esempio

```
✅ Documentazione Toduba Generata con Successo!

📊 Riepilogo Analisi:
- Tipo progetto: Full-stack (React + Express)
- File analizzati: 245
- Documenti generati: 8
- Tempo impiegato: 12s

📁 Documentazione disponibile in: ./docs/

📝 File generati:
- INDEX.md (Overview progetto)
- ARCHITECTURE.md (Architettura sistema)
- API_ENDPOINTS.md (25 endpoints)
- COMPONENTS.md (18 componenti React)
- DATABASE_SCHEMA.md (12 tabelle)

💡 Prossimi passi:
1. Revisiona la documentazione generata
2. Usa /toduba-update-docs per aggiornamenti futuri
3. Committa la cartella /docs nel repository
```

---

## 2. /toduba-update-docs 🔄

### Descrizione
Aggiornamento intelligente con cache, change detection avanzata e multiple export formats.

### Sintassi
```bash
/toduba-update-docs [--check] [--full] [--smart] [--format md|html|json|pdf]
```

### Argomenti
- `--check`: Mostra cosa verrebbe aggiornato senza modificare
- `--full`: Forza rigenerazione completa
- `--smart`: Abilita cache e ottimizzazioni (default: on)
- `--format`: Export format (md, html, json, pdf) - default: md

### Smart Cache System

**Change Detection**:
```javascript
detect_changes:
  - file_hash_comparison
  - git_diff_analysis
  - semantic_analysis
  - dependency_tracking

cache_strategy:
  - ttl: 7 days
  - max_size: 100MB
  - eviction: LRU
  - hit_rate_target: 75%
```

### Performance

| Metric | Without Cache | With Cache | Improvement |
|--------|---------------|------------|-------------|
| Update time | 45s | 3.2s | 93% faster |
| Memory usage | 500MB | 200MB | 60% reduction |
| File I/O ops | 1200 | 240 | 80% reduction |
| Cache hit rate | 0% | 78% | New feature |

### Export Formats

#### Markdown (default)
```bash
/toduba-update-docs --format md
```
- Base format
- Version controlled
- GitHub/GitLab compatible

#### HTML
```bash
/toduba-update-docs --format html
```
- Styled documentation
- Navigation menu
- Search functionality
- Responsive design

#### JSON
```bash
/toduba-update-docs --format json
```
- Structured data
- API integration
- Tool consumption
- Programmatic access

#### PDF
```bash
/toduba-update-docs --format pdf
```
- Print-ready
- Table of contents
- Page numbers
- Professional formatting

### Output Esempio

```
🔄 Toduba Smart Update - Analisi Modifiche

📊 Change Detection:
- File modificati: 8
- Commits dal'ultima doc: 5
- Ultimo commit: abc123 → def456

🎯 Update Strategy:
- API_ENDPOINTS.md: Update (3 endpoint modificati)
- COMPONENTS.md: Update (2 componenti nuovi)
- INDEX.md: Update (statistiche)
- Altri file: Skip (no changes)

⚡ Performance:
- Cache hit rate: 78%
- Estimated time: 3s (vs 45s full regen)
- Files to process: 8/42 (19%)

✅ Documentazione Aggiornata!
- Tempo impiegato: 3.2s
- Cache hits: 34/42 files
- Export format: Markdown
- Size: 2.3MB
```

---

## 3. /toduba-commit 💾

### Descrizione
Crea commit con messaggi strutturati seguendo best practices.

### Sintassi
```bash
/toduba-commit [message]
```

### Processo

1. **Analisi modifiche**
   ```bash
   git status
   git diff --staged
   git diff
   ```

2. **Suggerimento commit message**
   - Analizza natura modifiche
   - Identifica tipo (feat, fix, refactor, docs, etc.)
   - Genera messaggio descrittivo

3. **Conventional Commits Format**
   ```
   <type>(<scope>): <subject>

   <body>

   <footer>
   ```

### Commit Types

| Type | Descrizione | Esempio |
|------|-------------|---------|
| `feat` | Nuova feature | feat(api): add user authentication |
| `fix` | Bug fix | fix(ui): resolve button alignment issue |
| `refactor` | Code refactoring | refactor(db): optimize query performance |
| `docs` | Documentation | docs(readme): update installation steps |
| `test` | Test changes | test(api): add integration tests |
| `chore` | Maintenance | chore(deps): update dependencies |
| `style` | Code style | style(css): apply prettier formatting |
| `perf` | Performance | perf(api): add caching layer |

### Output Esempio

```
📝 Toduba Commit - Analisi Modifiche

🔍 Modifiche rilevate:
- src/api/users.ts (modified)
- src/api/auth.ts (new)
- tests/api/users.test.ts (new)

📊 Analisi:
- Tipo: Feature (nuova authentication)
- Scope: api
- Impact: Medium (2 file, 1 new)

✍️ Messaggio suggerito:

feat(api): implement JWT authentication

- Add login/logout endpoints
- Add JWT token generation and validation
- Add middleware for protected routes
- Add integration tests

Closes #42

🤔 Vuoi procedere con questo commit? [Y/n]
```

---

## 4. /toduba-code-review 🔍

### Descrizione
Esegue code review approfondita del codice.

### Sintassi
```bash
/toduba-code-review [file-or-directory] [--strict]
```

### Argomenti
- `file-or-directory`: Path da revieware (default: tutti i file modificati)
- `--strict`: Modalità strict con zero tolerance

### Review Checklist

#### Code Quality
- [ ] Naming conventions
- [ ] Code duplication
- [ ] Complexity (cyclomatic, cognitive)
- [ ] Dead code
- [ ] TODO/FIXME comments

#### Security
- [ ] Input validation
- [ ] SQL injection risks
- [ ] XSS vulnerabilities
- [ ] Hardcoded secrets
- [ ] Insecure dependencies

#### Performance
- [ ] N+1 queries
- [ ] Memory leaks
- [ ] Inefficient algorithms
- [ ] Missing indexes
- [ ] Large file sizes

#### Testing
- [ ] Test coverage >= 80%
- [ ] Edge cases covered
- [ ] Error handling tested
- [ ] Integration tests present

#### Best Practices
- [ ] SOLID principles
- [ ] DRY principle
- [ ] Error handling
- [ ] Logging
- [ ] Documentation

### Output Esempio

```
🔍 Toduba Code Review - src/api/

📊 Overview:
- File analizzati: 15
- Linee di codice: 2,458
- Issues totali: 12 (3 high, 5 medium, 4 low)

🔴 Issues HIGH priority:

1. src/api/users.ts:45
   ❌ SQL Injection Risk
   → Query usa string concatenation invece di prepared statements
   → Fix: Usa parametrized queries

2. src/api/auth.ts:23
   ❌ Hardcoded Secret
   → JWT secret hardcoded nel codice
   → Fix: Sposta in environment variable

3. src/api/payments.ts:67
   ❌ Missing Input Validation
   → Amount non validato prima di processare pagamento
   → Fix: Aggiungi validation con Joi/Zod

🟡 Issues MEDIUM priority:
[...]

📈 Code Quality Metrics:
- Complexity: 15 (medium)
- Maintainability: 68/100
- Test coverage: 72% (target: 80%)
- Duplication: 12%

💡 Recommendations:
1. Fix 3 high priority issues immediatamente
2. Increase test coverage da 72% a 80%
3. Refactor payments.ts (complexity 24 → troppo alto)
4. Add input validation middleware

⚠️ Review Status: REQUIRES CHANGES
```

---

## 5. /toduba-ultra-think 🧠

### Descrizione
Modalità di analisi profonda per problemi complessi.

### Sintassi
```bash
/toduba-ultra-think [problema o domanda]
```

### Modes

#### Quick Mode (30s)
Auto-detect per task semplici.

**Process**:
- Analisi rapida
- Solution straightforward
- No iterazioni

#### Standard Mode (2-5min)
Default per task normali.

**Process**:
1. Problem decomposition
2. Multiple perspectives
3. Solution exploration
4. Trade-off analysis
5. User confirmation

#### Deep Mode (5-15min)
Per task complessi.

**Process**:
1. Deep problem analysis
2. Root cause identification
3. Architecture exploration
4. Multiple solution paths
5. Risk assessment
6. Cost-benefit analysis
7. Implementation strategy
8. Multiple iterations con utente

### Output Esempio

```
🧠 Ultra-Think Analysis: "Optimize database performance"

📊 Mode Detection: DEEP (15min)
Reason: Performance optimization + architecture impact

🔍 Phase 1: Problem Analysis

Current State:
- Query response time: 2.3s average
- Peak load: 500 req/s
- Database: PostgreSQL 14
- Current indexes: 8

Pain Points:
- Slow user list query (2.8s)
- Dashboard timeout under load
- Reports generation > 10s

Root Causes:
1. Missing indexes on foreign keys
2. N+1 query pattern in ORM
3. No query result caching
4. No connection pooling optimization

🎯 Phase 2: Solution Exploration

Option A: Database Optimization
Pros:
- Quick wins con indexes
- No code changes needed
- Cost: Low

Cons:
- Partial solution
- Won't solve N+1 issue

Option B: Caching Layer (Redis)
Pros:
- Massive performance boost
- Handles peak load
- Scalable

Cons:
- Infrastructure cost
- Cache invalidation complexity
- Team learning curve

Option C: Full Refactor + Option B
Pros:
- Permanent solution
- Best performance
- Future-proof

Cons:
- High development time
- Risk of regression
- Cost: High

📈 Phase 3: Recommendation

Recommended: Hybrid Approach
1. Quick Wins (Week 1):
   - Add missing indexes
   - Fix N+1 queries
   - Expected gain: 60%

2. Medium Term (Week 2-3):
   - Implement Redis caching
   - Query optimization
   - Expected gain: 85%

3. Long Term (Month 2):
   - Architecture refactor
   - Expected gain: 95%

Total Cost: Medium
Total Time: 6 weeks
Risk Level: Low (incremental)

🤔 Vuoi procedere con questo approccio? [Y/n/modify]
```

---

## 6. /toduba-test 🧪

### Descrizione
Esegue test suite completa con report coverage e watch mode.

### Sintassi
```bash
/toduba-test [--watch] [--coverage] [--only <pattern>] [--fail-fast] [--parallel]
```

### Argomenti
- `--watch`: Watch mode per sviluppo continuo
- `--coverage`: Report coverage dettagliato
- `--only <pattern>`: Filtra test per pattern
- `--fail-fast`: Stop alla prima failure
- `--parallel`: Esecuzione parallela (3x faster)
- `--verbose`: Output dettagliato

### Processo

1. **Detection Framework**
   - Jest / Vitest → npm test
   - Pytest → pytest
   - Go → go test
   - Rust → cargo test

2. **Esecuzione**
   ```bash
   # Con coverage
   npm run test:coverage

   # Watch mode
   npm test -- --watch

   # Pattern matching
   npm test -- --testNamePattern="User"

   # Parallel
   npm test -- --maxWorkers=4
   ```

3. **Report Generation**
   - Console output
   - Coverage HTML
   - JUnit XML
   - Badge generation

### Output Esempio

```
🧪 Toduba Test Suite - Esecuzione

🔍 Test Framework: Jest
📂 Test directory: tests/
🎯 Mode: Coverage + Parallel

⚡ Esecuzione test in corso...

PASS tests/api/users.test.ts
  ✓ should create user (45ms)
  ✓ should validate email (12ms)
  ✓ should reject invalid data (8ms)

PASS tests/api/auth.test.ts
  ✓ should login with valid credentials (234ms)
  ✓ should reject invalid password (18ms)

FAIL tests/api/payments.test.ts
  ✗ should process payment (89ms)
    Error: Timeout exceeded

📊 Test Summary:
- Total: 42 tests
- Passed: 40 (95%)
- Failed: 2 (5%)
- Skipped: 0
- Duration: 12.5s

📈 Coverage Report:
File                | % Stmts | % Branch | % Funcs | % Lines
--------------------|---------|----------|---------|--------
All files           |   82.4  |   75.2   |   88.1  |   82.4
 api/users.ts       |   95.2  |   88.9   |   100   |   95.2
 api/auth.ts        |   78.5  |   66.7   |   80.0  |   78.5
 api/payments.ts    |   45.8  |   33.3   |   60.0  |   45.8

🎯 Coverage Threshold: 80%
Status: ✅ PASS (82.4% >= 80%)

❌ Failed Tests:
1. tests/api/payments.test.ts:45
   → Timeout: Payment processing > 5s
   → Suggestion: Increase timeout o mock external API

⚠️ Low Coverage Files:
- api/payments.ts: 45.8% (target: 80%)
  → Missing: Error handling paths
  → Missing: Refund flow tests

💡 Next Steps:
1. Fix 2 failed tests
2. Increase coverage di payments.ts
3. Consider adding e2e tests per payment flow
```

---

## 7. /toduba-rollback ⏮️

### Descrizione
Sistema di rollback con snapshot automatici per annullare modifiche.

### Sintassi
```bash
/toduba-rollback [--last] [--steps <n>] [--to <commit>] [--dry-run] [--force]
```

### Argomenti
- `--last`: Rollback all'ultimo snapshot
- `--steps <n>`: Rollback di n passi indietro
- `--to <commit>`: Rollback a commit specifico
- `--dry-run`: Mostra cosa verrebbe fatto senza eseguire
- `--force`: Skip confirmation prompts

### Snapshot System

**Auto-snapshot triggers**:
- Prima di ogni task complesso
- Prima di operazioni distruttive
- Su richiesta esplicita
- Pre-commit hook

**Snapshot storage**:
```
.toduba/snapshots/
├── snapshot_20251031_100523/
│   ├── metadata.json
│   ├── git_state.txt
│   └── files/
├── snapshot_20251031_143012/
└── snapshot_20251031_165521/
```

### Output Esempio

```
⏮️ Toduba Rollback - Lista Snapshot

📸 Snapshot Disponibili:

1. snapshot_20251031_165521 (2 ore fa)
   Branch: feature/payments
   Commit: def456
   Descrizione: Prima di implementare payment refactor
   Files: 23 modified
   Size: 2.4MB

2. snapshot_20251031_143012 (5 ore fa)
   Branch: feature/payments
   Commit: abc123
   Descrizione: Prima di aggiungere validation
   Files: 8 modified
   Size: 856KB

3. snapshot_20251031_100523 (8 ore fa)
   Branch: main
   Commit: xyz789
   Descrizione: Backup automatico pre-commit
   Files: 15 modified
   Size: 1.2MB

🎯 Seleziona snapshot da ripristinare: [1/2/3/cancel]

> 1

⚠️ Rollback Preview (--dry-run):

File Changes:
- src/api/payments.ts: Revert to version def456
- src/api/refunds.ts: DELETE (non esisteva)
- tests/payments.test.ts: Revert to version def456

Git State:
- Current: abc123 (2 commits ahead)
- Target: def456
- Action: git reset --hard def456

🤔 Confermi rollback? [Y/n]

> Y

✅ Rollback Completato!
- Ripristinati: 23 file
- Git reset: def456
- Backup pre-rollback: snapshot_20251031_170015
- Tempo impiegato: 2.3s

💡 Per annullare questo rollback:
/toduba-rollback --to snapshot_20251031_170015
```

---

## 8. /toduba-help 📖

### Descrizione
Sistema di help integrato con esempi e documentazione contestuale.

### Sintassi
```bash
/toduba-help [command|agent] [--examples] [--verbose]
```

### Argomenti
- `command|agent`: Nome comando o agente specifico
- `--examples`: Mostra esempi d'uso
- `--verbose`: Output dettagliato con tutte le opzioni

### Output Esempio

```
📖 Toduba Help System

/toduba-help                    → Questo help generale
/toduba-help <command>          → Help per comando specifico
/toduba-help <agent>            → Help per agente specifico

📝 Comandi Disponibili:

Core Commands:
  /toduba-init                  → Genera documentazione iniziale
  /toduba-update-docs           → Aggiorna docs incrementalmente
  /toduba-commit                → Commit strutturato
  /toduba-code-review           → Code review approfondita

Development:
  /toduba-test                  → Esegue test suite
  /toduba-interactive           → Modalità step-by-step
  /toduba-template              → Scaffolding template

Analysis:
  /toduba-ultra-think           → Analisi profonda problemi

Utilities:
  /toduba-rollback              → Sistema di rollback
  /toduba-help                  → Questo help

🤖 Agenti Disponibili:
  toduba-orchestrator           → Coordinamento centrale
  toduba-backend-engineer       → Backend & API
  toduba-frontend-engineer      → Frontend & UI/UX
  toduba-mobile-engineer        → Flutter/Mobile
  toduba-qa-engineer            → Test execution
  toduba-test-engineer          → Test writing
  toduba-codebase-analyzer      → Code analysis
  toduba-documentation-generator → Docs generation

💡 Quick Start:
1. /toduba-init                 → Inizializza progetto
2. /toduba-ultra-think "task"   → Analizza task
3. [Orchestrator coordina]      → Implementazione
4. /toduba-test --coverage      → Verifica qualità
5. /toduba-commit               → Committa modifiche

📚 Per help dettagliato:
/toduba-help init               → Help per /toduba-init
/toduba-help orchestrator       → Help per agente orchestrator
/toduba-help --examples         → Mostra esempi pratici
```

---

## 9. /toduba-interactive 🎮

### Descrizione
Modalità interattiva con esecuzione step-by-step e controllo utente.

### Sintassi
```bash
/toduba-interactive [--step-by-step] [--auto-pause] [--verbose]
```

### Argomenti
- `--step-by-step`: Conferma ogni step prima di eseguire
- `--auto-pause`: Pausa automatica dopo ogni agente
- `--verbose`: Output dettagliato di ogni operazione

### Controlli

| Key | Azione | Descrizione |
|-----|--------|-------------|
| `[Enter]` | Continue | Procedi al prossimo step |
| `[p]` | Pause | Metti in pausa esecuzione |
| `[s]` | Skip | Salta step corrente |
| `[u]` | Undo | Annulla ultimo step |
| `[c]` | Checkpoint | Crea restore point |
| `[i]` | Inspect | Mostra stato corrente |
| `[q]` | Quit | Esci (con conferma) |

### Features

**Step-by-step execution**:
- Mostra plan completo prima di iniziare
- Chiede conferma per ogni step
- Possibilità di modificare plan durante esecuzione

**Pause/Resume**:
- Pausa in qualsiasi momento
- Salva stato per ripresa futura
- Persistent across sessions

**Undo**:
- Annulla ultimo step eseguito
- Stack di undo fino a 10 steps
- Automatic snapshot creation

**Checkpoints**:
- Crea restore point manualmente
- Quick rollback a checkpoint
- Named checkpoints

### Output Esempio

```
🎮 Toduba Interactive Mode

📋 Plan Overview:
1. [ ] Analizza requisiti feature "user notifications"
2. [ ] Backend: Implementa notification API
3. [ ] Frontend: Crea NotificationBell component
4. [ ] Test: Scrive integration tests
5. [ ] QA: Esegue test suite
6. [ ] Docs: Aggiorna documentazione

⚙️ Controlli:
[Enter] Continue | [p] Pause | [s] Skip | [u] Undo | [c] Checkpoint | [q] Quit

═══════════════════════════════════════════════════════

📍 Step 1/6: Analizza requisiti

🎯 Azione: Ultra-think analysis (standard mode)
⏱️ Tempo stimato: 2 minuti

🤔 Vuoi procedere con questo step? [Y/n/skip/pause]

> Y

🔄 Esecuzione in corso...

✅ Step 1 completato!

Risultati:
- Identificate 3 varianti di notification
- Definiti 5 endpoint API necessari
- Stimato tempo implementazione: 4 ore

💾 Auto-checkpoint creato: step_1_complete

═══════════════════════════════════════════════════════

📍 Step 2/6: Backend - Notification API

🎯 Agente: toduba-backend-engineer
📦 Work package: backend_notifications_api.md
⏱️ Tempo stimato: 1.5 ore

Work Package Preview:
- Endpoint: POST /api/notifications/send
- Endpoint: GET /api/notifications/user/:id
- Endpoint: PATCH /api/notifications/:id/read
- Database: notifications table
- Real-time: WebSocket support

🤔 Vuoi procedere? [Y/n/skip/pause/inspect]

> i

🔍 Inspection Mode:

Current State:
- Completed: 1/6 steps
- Active checkpoint: step_1_complete
- Undo stack: [step_1]
- Time elapsed: 2m 15s
- Estimated remaining: 3h 45m

Modified Files: 0
Test Coverage: 82.4%

[Enter per tornare] >

🤔 Vuoi procedere? [Y/n/skip/pause/inspect]

> Y

🔄 Esecuzione backend-engineer...

[Output backend engineer...]

✅ Step 2 completato!

💾 Auto-checkpoint: step_2_complete

[Continua con step 3...]
```

---

## 10. /toduba-template 📋

### Descrizione
Sistema di template per workflow comuni e scaffolding rapido.

### Sintassi
```bash
/toduba-template [template-name] [--list] [--variables key=value]
```

### Argomenti
- `template-name`: Nome template da usare
- `--list`: Lista template disponibili
- `--variables`: Passa variabili al template (es: --variables name=User,plural=Users)

### Template Disponibili

#### 1. CRUD API
```bash
/toduba-template crud-api --variables name=User,plural=Users
```

Genera:
- `src/api/users/routes.ts` - Express routes
- `src/api/users/controller.ts` - Business logic
- `src/api/users/model.ts` - Database model
- `src/api/users/validation.ts` - Joi schemas
- `tests/api/users.test.ts` - Integration tests

#### 2. React Component
```bash
/toduba-template react-component --variables name=Header,type=layout
```

Genera:
- `src/components/Header/Header.tsx` - Component
- `src/components/Header/Header.styles.ts` - Styled components
- `src/components/Header/Header.test.tsx` - Jest tests
- `src/components/Header/Header.stories.tsx` - Storybook
- `src/components/Header/index.ts` - Export

#### 3. Flutter Screen
```bash
/toduba-template flutter-screen --variables name=Profile
```

Genera:
- `lib/screens/profile/profile_screen.dart` - Screen widget
- `lib/screens/profile/profile_controller.dart` - Riverpod controller
- `lib/screens/profile/profile_state.dart` - State model
- `test/screens/profile_test.dart` - Widget tests

#### 4. Microservice
```bash
/toduba-template microservice --variables name=payments
```

Genera:
- Project structure completa
- Dockerfile + docker-compose
- API endpoints base
- Database setup
- Testing setup
- CI/CD config

#### 5. Auth Flow
```bash
/toduba-template auth-flow --variables method=jwt
```

Genera:
- Backend auth endpoints
- Frontend auth context
- Protected route wrapper
- Login/register forms
- JWT middleware
- Refresh token logic

### Custom Templates

Crea template personalizzati in `templates/`:

```
templates/
├── crud-api/
│   ├── template.json
│   └── files/
│       ├── routes.ts.template
│       └── model.ts.template
└── my-custom-template/
    ├── template.json
    └── files/
```

**template.json**:
```json
{
  "name": "crud-api",
  "description": "Complete CRUD API with tests",
  "variables": {
    "name": {
      "type": "string",
      "required": true,
      "description": "Entity name (singular)"
    },
    "plural": {
      "type": "string",
      "required": true,
      "description": "Entity name (plural)"
    }
  },
  "files": [
    {
      "template": "routes.ts.template",
      "output": "src/api/{{plural}}/routes.ts"
    }
  ]
}
```

### Output Esempio

```
📋 Toduba Template - CRUD API

📝 Template: crud-api
📦 Entity: User (Users)

🔍 Preview Files:
- src/api/users/routes.ts (245 lines)
- src/api/users/controller.ts (189 lines)
- src/api/users/model.ts (67 lines)
- src/api/users/validation.ts (42 lines)
- tests/api/users.test.ts (156 lines)

Total: 5 files, 699 lines

🤔 Vuoi procedere? [Y/n]

> Y

✅ Template Applicato!

📁 File creati:
✓ src/api/users/routes.ts
✓ src/api/users/controller.ts
✓ src/api/users/model.ts
✓ src/api/users/validation.ts
✓ tests/api/users.test.ts

📝 Next Steps:
1. Customizza model in users/model.ts
2. Aggiungi business logic in users/controller.ts
3. Run tests: npm test tests/api/users.test.ts
4. Start server: npm run dev
5. Test API: curl http://localhost:3000/api/users

💡 Endpoints disponibili:
- GET    /api/users
- GET    /api/users/:id
- POST   /api/users
- PUT    /api/users/:id
- DELETE /api/users/:id

🔗 Documentazione: http://localhost:3000/api-docs
```

---

## 🔗 Command Chaining

Molti comandi possono essere concatenati per workflow complessi:

```bash
# Workflow completo
/toduba-init && \
/toduba-test --coverage && \
/toduba-code-review && \
/toduba-commit && \
/toduba-update-docs

# Development loop
/toduba-interactive --step-by-step && \
/toduba-test --watch

# Release workflow
/toduba-test --coverage && \
/toduba-code-review --strict && \
/toduba-update-docs --format pdf && \
/toduba-commit "Release v2.0"
```

---

## 📊 Command Usage Matrix

| Scenario | Commands | Order |
|----------|----------|-------|
| **New Project** | init → template → interactive | 1 → 2 → 3 |
| **Feature Development** | ultra-think → interactive → test → commit | 1 → 2 → 3 → 4 |
| **Bug Fix** | code-review → rollback → test | 1 → 2 → 3 |
| **Refactoring** | ultra-think (deep) → code-review → test → update-docs | 1 → 2 → 3 → 4 |
| **Release Prep** | test → code-review → update-docs → commit | 1 → 2 → 3 → 4 |

---

**Documento generato da**: Toduba Documentation Generator
**Versione**: 2.0.0
**Data**: 2025-10-31
