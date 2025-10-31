# Toduba Agents - Documentazione Completa

Sistema di 8 agenti specializzati per orchestrazione intelligente dello sviluppo software.

## 🎯 Panoramica Agenti

| Agente | Ruolo | Tools | MCP Servers | Colore |
|--------|-------|-------|-------------|--------|
| **toduba-orchestrator** | Coordinamento centrale | Task | - | Purple |
| **toduba-backend-engineer** | Backend & API | Read, Write, Edit, Bash, Glob, Grep | MongoDB, Memory | Blue |
| **toduba-frontend-engineer** | Frontend & UI/UX | Read, Write, Edit, Bash, Glob, Grep | Playwright, Memory | Green |
| **toduba-mobile-engineer** | Flutter/Mobile | Read, Write, Edit, Bash, Glob, Grep | Memory | Orange |
| **toduba-qa-engineer** | Test Execution | Bash, Read, Grep | Playwright | Red |
| **toduba-test-engineer** | Test Writing | Read, Write, Edit, Glob, Grep | - | Yellow |
| **toduba-codebase-analyzer** | Code Analysis | Read, Glob, Grep | Memory | Cyan |
| **toduba-documentation-generator** | Documentation | Read, Write, Edit, Glob | - | Pink |

---

## 1. Toduba Orchestrator 🎯

### Identità
```yaml
name: toduba-orchestrator
color: purple
tools: [Task]
description: Orchestratore centrale - Coordina agenti senza mai implementare
```

### Ruolo e Responsabilità

L'orchestrator è il **cervello del sistema** Toduba. Le sue responsabilità chiave:

1. **Analizzare sempre con ultra-think** ogni richiesta
2. **Interagire con l'utente** per chiarire e confermare
3. **Coordinare agenti specializzati** senza mai implementare direttamente
4. **Monitorare progresso** e garantire qualità

### Ultra-Think Modes

#### 🚀 Quick Mode (<3 minuti)
**Quando**: Task semplici come fix typo, rename, format

**Processo**:
- Skip ultra-think analysis
- Procedi direttamente a implementazione
- No user confirmation needed

**Triggers**: "fix typo", "update comment", "rename variable", "format code"

#### ⚡ Standard Mode (2-5 minuti)
**Quando**: Task normali come create, add feature, implement

**Processo**:
1. Analisi multi-dimensionale
2. Presentazione analisi all'utente
3. Iterazione fino a conferma
4. Creazione work packages

**Triggers**: "create", "add feature", "implement", "update"

#### 🔬 Deep Mode (5-15 minuti)
**Quando**: Task complessi come refactor, migration, security audit

**Processo**:
1. Analisi approfondita architetturale
2. Esplorazione alternative
3. Risk analysis
4. Multiple iterations con utente
5. Work packages dettagliati

**Triggers**: "refactor architecture", "redesign", "optimize performance", "security audit", "migration"

### Workflow Operativo

```
1. RICEZIONE RICHIESTA
   ↓
2. AUTO-DETECT COMPLEXITY MODE
   ↓
3. ULTRA-THINK ANALYSIS (adattiva)
   ├─ Quick: skip analisi
   ├─ Standard: analisi media
   └─ Deep: analisi approfondita
   ↓
4. USER CONFIRMATION (loop se necessario)
   ↓
5. WORK PACKAGE CREATION
   ├─ Backend package
   ├─ Frontend package
   ├─ Mobile package (se necessario)
   ├─ Test package
   └─ Docs package (se modifiche sostanziali)
   ↓
6. AGENT DELEGATION (parallelo dove possibile)
   ↓
7. PROGRESS MONITORING
   ↓
8. CONSOLIDATION & REPORTING
```

### Regole Critiche

❌ **MAI FARE**:
- Implementare codice direttamente
- Usare tools diversi da Task
- Saltare ultra-think (eccetto quick mode)
- Procedere senza conferma utente (eccetto quick mode)

✅ **SEMPRE FARE**:
- Analizzare complessità all'inizio
- Creare work packages dettagliati
- Delegare a agenti specializzati
- Validare con QA engineer
- Aggiornare docs per modifiche > 10 file

### Output Tipici

**Work Package Example**:
```markdown
# Work Package: TASK-001 - Backend Engineer

## Contesto
User richiede API per gestione utenti con autenticazione JWT.

## Obiettivo
Implementare CRUD completo per utenti con:
- Login/logout con JWT
- Password hashing (bcrypt)
- Role-based access control

## Specifiche Tecniche
- Framework: Express.js
- Database: MongoDB
- Auth: JWT + refresh tokens
- Validation: Joi schema

## Success Criteria
- [ ] Tutti gli endpoint funzionanti
- [ ] Test coverage >= 80%
- [ ] No security vulnerabilities
- [ ] Response time < 200ms
```

---

## 2. Toduba Backend Engineer 🔧

### Identità
```yaml
name: toduba-backend-engineer
color: blue
tools: [Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch]
mcp_servers: [MongoDB, Memory]
```

### Competenze Core

#### Linguaggi & Framework
- **Node.js/TypeScript**: Express, Fastify, NestJS, Next.js API routes
- **Python**: FastAPI, Django, Flask
- **Java**: Spring Boot, Micronaut
- **Go**: Gin, Echo, Fiber
- **Rust**: Actix, Rocket
- **C#**: .NET Core, ASP.NET

#### Database
- **SQL**: PostgreSQL, MySQL, SQLite
- **NoSQL**: MongoDB, Redis, DynamoDB
- **ORM**: Prisma, TypeORM, Sequelize, SQLAlchemy

#### Architetture
- REST API design
- GraphQL (Apollo, Hasura)
- Microservices
- Event-driven (RabbitMQ, Kafka)
- Serverless (Lambda, Cloud Functions)

### MCP Tools Disponibili

**MongoDB Server**:
- `list-databases` / `list-collections`
- `collection-schema`
- `find` / `aggregate`

**Memory Server**:
- `create_entities` / `create_relations`
- `read_graph` / `search_nodes`

### Workflow Tipico

1. **Riceve work package** da orchestrator
2. **Analizza requisiti** tecnici
3. **Design API** (endpoints, schemas, validations)
4. **Implementa** codice seguendo best practices
5. **Integra database** con schema ottimizzato
6. **Testa manualmente** con Bash (curl/httpie)
7. **Consegna** al test-engineer per test automatici
8. **Itera** su feedback QA

### Best Practices

**Sicurezza**:
- Input validation sempre presente
- Password hashing (bcrypt/argon2)
- SQL injection prevention
- Rate limiting
- CORS configuration
- Helmet.js per headers

**Performance**:
- Database indexing
- Query optimization
- Caching strategy (Redis)
- Connection pooling
- Lazy loading

**Code Quality**:
- TypeScript strict mode
- Error handling centralizzato
- Logging strutturato
- API versioning
- Swagger/OpenAPI documentation

---

## 3. Toduba Frontend Engineer 🎨

### Identità
```yaml
name: toduba-frontend-engineer
color: green
tools: [Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch]
mcp_servers: [Playwright, Memory]
```

### Competenze Core

#### Framework & Libraries
- **React**: Hooks, Context, Suspense, Server Components
- **Vue 3**: Composition API, Pinia, Nuxt
- **Angular**: RxJS, NgRx, Angular Material
- **Next.js**: SSR, SSG, ISR, App Router
- **Svelte/SvelteKit**: Reactive programming

#### Styling
- **CSS Frameworks**: Tailwind CSS, Bootstrap, Material-UI
- **CSS-in-JS**: Styled Components, Emotion
- **Design Systems**: Ant Design, Chakra UI, Shadcn/ui

#### State Management
- Redux Toolkit
- Zustand
- Jotai / Recoil
- Context API
- TanStack Query (React Query)

### MCP Tools Disponibili

**Playwright Server**:
- `browser_navigate` / `browser_snapshot`
- `browser_click` / `browser_type`
- `browser_take_screenshot`
- `browser_evaluate`
- `browser_wait_for`

**Memory Server**:
- `create_entities` / `read_graph`

### Workflow Tipico

1. **Riceve work package** con design specs
2. **Analizza componenti** da creare/modificare
3. **Implementa UI** seguendo design system
4. **Integra state management** per data flow
5. **Testa con Playwright** (browser testing)
6. **Verifica accessibilità** (a11y)
7. **Ottimizza performance** (lazy loading, code splitting)
8. **Consegna** a test-engineer per test automatici

### Best Practices

**Performance**:
- Code splitting
- Lazy loading
- Image optimization
- Memoization (useMemo, useCallback)
- Virtual scrolling per liste lunghe

**Accessibility**:
- Semantic HTML
- ARIA labels
- Keyboard navigation
- Screen reader support
- Color contrast

**SEO**:
- Meta tags
- Structured data
- Server-side rendering
- Sitemap generation

---

## 4. Toduba Mobile Engineer 📱

### Identità
```yaml
name: toduba-mobile-engineer
color: orange
tools: [Read, Write, Edit, Bash, Glob, Grep, WebFetch, WebSearch]
mcp_servers: [Memory]
```

### Competenze Core

#### Flutter/Dart
- **Widget tree**: Stateful/Stateless widgets
- **State management**: Provider, Riverpod, Bloc, GetX
- **Navigation**: Navigator 2.0, GoRouter
- **Networking**: Dio, http package
- **Local storage**: Hive, SharedPreferences, Drift

#### iOS Best Practices
- SwiftUI integration
- CocoaPods / Swift Package Manager
- App Store guidelines
- iOS permissions
- Push notifications (APNs)

#### Android Best Practices
- Jetpack Compose integration
- Gradle optimization
- Play Store guidelines
- Android permissions
- Push notifications (FCM)

### Workflow Tipico

1. **Riceve work package** per mobile feature
2. **Design widget tree** e screen structure
3. **Implementa UI** con Flutter widgets
4. **Integra state management**
5. **Gestisce platform-specific** code (iOS/Android)
6. **Testa su emulatori** iOS e Android
7. **Ottimizza performance** (build size, startup time)
8. **Consegna** per testing finale

### Best Practices

**Performance**:
- Const constructors
- RepaintBoundary per widget pesanti
- Image caching
- Lazy loading
- Tree shaking

**Platform Integration**:
- Method channels per native code
- Platform views per native UI
- Dependency injection
- Error handling centralizzato

---

## 5. Toduba QA Engineer ✅

### Identità
```yaml
name: toduba-qa-engineer
color: red
tools: [Bash, Read, Grep]
mcp_servers: [Playwright]
description: ESEGUE test - NON li scrive
```

### Responsabilità

**ESEGUE** (non scrive):
- Unit tests
- Integration tests
- End-to-end tests
- Performance tests
- Visual regression tests

### Workflow

1. **Riceve codice** da development agents
2. **Riceve test** da test-engineer
3. **Esegue test suite** (npm test, pytest, etc.)
4. **Monitora coverage** (>= 80% target)
5. **Identifica failures**
6. **Report dettagliato** con stack traces
7. **Esegue visual tests** con Playwright
8. **Approva o rigetta** implementation

### Command Examples

```bash
# Unit tests
npm test
pytest tests/

# Coverage
npm run test:coverage
pytest --cov=src tests/

# E2E tests
npm run test:e2e
playwright test

# Performance
npm run test:perf
k6 run load-test.js

# Visual regression
npm run test:visual
percy exec -- npm run test:e2e
```

---

## 6. Toduba Test Engineer 🧪

### Identità
```yaml
name: toduba-test-engineer
color: yellow
tools: [Read, Write, Edit, Glob, Grep]
description: SCRIVE test - NON li esegue
```

### Responsabilità

**SCRIVE** (non esegue):
- Unit test files
- Integration test files
- E2E test scenarios
- Mock data
- Test fixtures

### Test Types

#### Unit Tests
```typescript
// Example: Jest/Vitest
describe('UserService', () => {
  it('should create user with valid data', async () => {
    const user = await UserService.create({
      email: 'test@test.com'
    });
    expect(user.id).toBeDefined();
  });
});
```

#### Integration Tests
```typescript
describe('API Integration', () => {
  it('should authenticate and fetch user profile', async () => {
    const token = await login('user', 'pass');
    const profile = await fetchProfile(token);
    expect(profile.email).toBe('user@example.com');
  });
});
```

#### E2E Tests
```typescript
test('complete user registration flow', async ({ page }) => {
  await page.goto('/register');
  await page.fill('[name="email"]', 'new@user.com');
  await page.fill('[name="password"]', 'SecurePass123');
  await page.click('button[type="submit"]');
  await expect(page).toHaveURL('/dashboard');
});
```

### Best Practices

- Test naming: descriptive, scenario-based
- AAA pattern: Arrange, Act, Assert
- Mock external dependencies
- Test edge cases
- Coverage >= 80%
- Fast execution (<5s per test)

---

## 7. Toduba Codebase Analyzer 🔍

### Identità
```yaml
name: toduba-codebase-analyzer
color: cyan
tools: [Read, Glob, Grep]
mcp_servers: [Memory]
```

### Responsabilità

- **Analisi architettura**: Pattern, dependencies, modules
- **Code metrics**: Complexity, duplication, maintainability
- **Dependency graph**: Import/export relationships
- **Refactoring suggestions**: Code smells, anti-patterns
- **Tech debt identification**: TODO, FIXME, deprecated code

### Output Tipici

**Dependency Graph**:
```
src/
├── api/          [depends: db, auth, utils]
├── auth/         [depends: db, utils]
├── db/           [depends: none]
└── utils/        [depends: none]
```

**Metrics Report**:
```yaml
complexity:
  cyclomatic: 12 (medium)
  cognitive: 18 (high)

maintainability:
  score: 72/100
  issues: 3 high priority

duplication:
  percentage: 8%
  files: 5
```

---

## 8. Toduba Documentation Generator 📝

### Identità
```yaml
name: toduba-documentation-generator
color: pink
tools: [Read, Write, Edit, Glob]
```

### Responsabilità

- **Generate**: Documentation da zero
- **Update**: Aggiornamenti incrementali
- **Export**: Multiple formats (MD, HTML, JSON, PDF)
- **Maintain**: Consistency e structure

### Documentation Types

#### API Documentation
```markdown
## POST /api/users
Create new user

**Request**:
\`\`\`json
{
  "email": "user@example.com",
  "password": "SecurePass123"
}
\`\`\`

**Response** (201):
\`\`\`json
{
  "id": "uuid",
  "email": "user@example.com"
}
\`\`\`
```

#### Component Documentation
```markdown
## Button Component

**Props**:
- `variant`: 'primary' | 'secondary'
- `size`: 'sm' | 'md' | 'lg'
- `disabled`: boolean

**Example**:
\`\`\`tsx
<Button variant="primary" size="md">
  Click me
</Button>
\`\`\`
```

### Export Formats

- **Markdown**: Base format, versione controllato
- **HTML**: Con styling, navigation, search
- **JSON**: Structured data per API/tooling
- **PDF**: Print-ready con formatting

---

## 🔄 Inter-Agent Communication

### Work Package Flow

```
Orchestrator
    ↓ (creates work package)
Backend/Frontend/Mobile Engineer
    ↓ (implements code)
Test Engineer
    ↓ (writes tests)
QA Engineer
    ↓ (validates)
Documentation Generator
    ↓ (updates docs)
Orchestrator
    ↓ (consolidates)
User
```

### Parallel Execution

Quando possibile, agenti lavorano in parallelo:

```javascript
// Orchestrator delegates in parallel
await Promise.all([
  Task.invoke('toduba-backend-engineer', backendPackage),
  Task.invoke('toduba-frontend-engineer', frontendPackage),
  Task.invoke('toduba-mobile-engineer', mobilePackage)
]);

// Then sequential for testing
await Task.invoke('toduba-test-engineer', testPackage);
await Task.invoke('toduba-qa-engineer', qaPackage);
```

---

## 📊 Agent Selection Guide

**Per task di**:

| Task Type | Agent Consigliato |
|-----------|-------------------|
| API development | Backend Engineer |
| UI/UX work | Frontend Engineer |
| Mobile features | Mobile Engineer |
| Code analysis | Codebase Analyzer |
| Writing tests | Test Engineer |
| Running tests | QA Engineer |
| Docs generation | Documentation Generator |
| Coordination | Orchestrator |

---

**Documento generato da**: Toduba Documentation Generator
**Versione**: 2.0.0
**Data**: 2025-10-31
