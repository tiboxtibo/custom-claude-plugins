---
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
argument-hint: "[--check] [--full] [--smart] [--service <name>] [--format md|html|json|pdf]"
description: "Smart incremental updates con cache e multiple export formats per struttura gerarchica V2.0"
---

# Toduba Update Docs V2.0 - Smart Incremental Updates 🔄

## Obiettivo

Aggiornamento intelligente e incrementale della documentazione gerarchica V2.0 (docs/global, docs/services/, docs/operations/) con cache, change detection avanzata e supporto per multiple export formats.

## Argomenti

- `--check`: Mostra cosa verrebbe aggiornato senza modificare
- `--full`: Forza rigenerazione completa (equivalente a toduba-init --force)
- `--smart`: Abilita cache e ottimizzazioni AI (default: on)
- `--service <name>`: Aggiorna solo documentazione per il servizio specificato
- `--format`: Formato export (md, html, json, pdf) - default: md

Argomenti ricevuti: $ARGUMENTS

## Nuova Struttura Supportata (V2.0)

```
docs/
├── .toduba-meta/              # Metadata e tracking
│   ├── project-type.json
│   ├── services.json
│   ├── last-update.json
│   └── service_*.json
│
├── global/                    # Documentazione globale
│   ├── README.md
│   ├── ARCHITECTURE.md
│   ├── SETUP.md
│   ├── CONTRIBUTING.md
│   └── adr/
│
├── services/                  # Per-service documentation
│   └── [service-name]/
│       ├── README.md
│       ├── SETUP.md
│       ├── ARCHITECTURE.md
│       ├── TECH-STACK.md
│       ├── STYLE-GUIDE.md
│       ├── ENDPOINTS.md (condizionale)
│       ├── DATABASE.md (condizionale)
│       ├── TESTING.md
│       └── TROUBLESHOOTING.md
│
└── operations/                # DevOps docs
    ├── DEPLOYMENT.md
    ├── CI-CD.md
    ├── MONITORING.md
    ├── SECURITY.md
    └── ENVIRONMENT-VARS.md
```

## Pre-requisiti

```bash
# Verifica che docs/ esista
if [ ! -d "docs" ]; then
  echo "❌ Errore: Documentazione non trovata!"
  echo "   Esegui prima: /toduba-system:toduba-init"
  exit 1
fi

# Verifica nuova struttura V2.0
if [ ! -d "docs/.toduba-meta" ]; then
  echo "⚠️ Struttura documentazione V1.0 rilevata!"
  echo "   Aggiorna alla V2.0 con: /toduba-system:toduba-init --force"
  echo "   (La vecchia documentazione verrà backuppata automaticamente)"
  exit 1
fi

# Verifica metadata essenziali
if [ ! -f "docs/.toduba-meta/last-update.json" ]; then
  echo "⚠️ Metadata mancante - rigenerazione completa necessaria"
  echo "   Esegui: /toduba-system:toduba-init --force"
  exit 1
fi
```

## Processo di Aggiornamento Intelligente V2.0

### Fase 1: Analisi Cambiamenti (Struttura Gerarchica)

#### 1.1 Lettura Stato Precedente

```bash
# Leggi metadata V2.0
LAST_COMMIT=$(cat docs/.toduba-meta/last-update.json | grep -o '"git_commit": *"[^"]*"' | cut -d'"' -f4)
LAST_UPDATE=$(cat docs/.toduba-meta/last-update.json | grep -o '"timestamp": *"[^"]*"' | cut -d'"' -f4)
PROJECT_TYPE=$(cat docs/.toduba-meta/project-type.json | grep -o '"type": *"[^"]*"' | cut -d'"' -f4)

# Leggi lista servizi
SERVICES_LIST=$(cat docs/.toduba-meta/services.json | grep -o '"name": *"[^"]*"' | cut -d'"' -f4)

echo "📊 Stato precedente:"
echo "   • Ultimo aggiornamento: $LAST_UPDATE"
echo "   • Ultimo commit: ${LAST_COMMIT:0:7}"
echo "   • Tipo progetto: $PROJECT_TYPE"
echo "   • Servizi: $(echo "$SERVICES_LIST" | wc -l)"
```

#### 1.2 Calcolo Differenze

```bash
# Commits dal'ultimo update
COMMITS_COUNT=$(git rev-list --count ${LAST_COMMIT}..HEAD)

# File modificati raggruppati per categoria
git diff --name-only ${LAST_COMMIT}..HEAD | while read file; do
  case "$file" in
    */api/* | */routes/* | */controllers/*)
      echo "API: $file" >> changes_api.txt
      ;;
    */components/* | */pages/* | */views/*)
      echo "FRONTEND: $file" >> changes_frontend.txt
      ;;
    */models/* | */schemas/* | */migrations/*)
      echo "DATABASE: $file" >> changes_db.txt
      ;;
    *.test.* | *.spec.* | */tests/*)
      echo "TESTS: $file" >> changes_tests.txt
      ;;
  esac
done
```

### Fase 2: Decisione Aggiornamento (Struttura Gerarchica V2.0)

#### Matrice di Update per Struttura V2.0:

```
Cambiamenti rilevati → Documenti da aggiornare
────────────────────────────────────────────────────────
GLOBAL SCOPE:
- Root files changes          → docs/global/README.md
- Architecture changes        → docs/global/ARCHITECTURE.md
- Contributing changes        → docs/global/CONTRIBUTING.md
- Setup changes (monorepo)    → docs/global/SETUP.md

SERVICE SCOPE (per ogni servizio modificato):
- Source code changes         → docs/services/[name]/ARCHITECTURE.md
- API/Routes changes          → docs/services/[name]/ENDPOINTS.md
- Database/models changes     → docs/services/[name]/DATABASE.md
- Dependencies changes        → docs/services/[name]/TECH-STACK.md
- Test changes               → docs/services/[name]/TESTING.md
- Style/conventions          → docs/services/[name]/STYLE-GUIDE.md
- Any service changes        → docs/services/[name]/README.md

OPERATIONS SCOPE:
- CI/CD config changes        → docs/operations/CI-CD.md
- Deployment scripts         → docs/operations/DEPLOYMENT.md
- Monitoring config          → docs/operations/MONITORING.md
- Security policies          → docs/operations/SECURITY.md
- Env vars changes           → docs/operations/ENVIRONMENT-VARS.md

METADATA (always):
- docs/.toduba-meta/last-update.json
- docs/.toduba-meta/service_*.json (se servizio modificato)
```

#### Rilevamento Servizio da File Modificato

```bash
detect_affected_service() {
  local file_path="$1"

  # Leggi servizi e i loro path
  while IFS= read -r service_name; do
    service_path=$(cat "docs/.toduba-meta/service_${service_name}.json" | grep -o '"path": *"[^"]*"' | cut -d'"' -f4)

    # Se il file è nel path del servizio
    if [[ "$file_path" == "$service_path"* ]]; then
      echo "$service_name"
      return
    fi
  done <<< "$SERVICES_LIST"

  # Se non trovato, è probabilmente global
  echo "global"
}
```

#### Soglie per Update:

- **Minor** (< 5 file): Aggiorna solo file specifici
- **Medium** (5-20 file): Aggiorna categoria + INDEX
- **Major** (> 20 file): Considera update completo
- **Structural** (nuove cartelle/moduli): ARCHITECTURE.md obbligatorio

### Fase 3: Update Incrementale

#### 3.1 Per API_ENDPOINTS.md:

```javascript
// Analizza solo endpoint modificati
const modifiedControllers = getModifiedFiles("controllers");
const modifiedRoutes = getModifiedFiles("routes");

// Estrai endpoint esistenti
const existingEndpoints = parseExistingEndpoints("API_ENDPOINTS.md");

// Analizza nuovi/modificati
const updatedEndpoints = analyzeEndpoints(modifiedControllers, modifiedRoutes);

// Merge intelligente
const mergedEndpoints = mergeEndpoints(existingEndpoints, updatedEndpoints);

// Rigenera solo sezioni cambiate
updateSections("API_ENDPOINTS.md", mergedEndpoints);
```

#### 3.2 Per COMPONENTS.md:

```javascript
// Simile approccio per componenti UI
const modifiedComponents = getModifiedFiles(["components", "pages"]);

// Aggiorna solo componenti modificati
for (const component of modifiedComponents) {
  const componentDoc = generateComponentDoc(component);
  replaceSection("COMPONENTS.md", component.name, componentDoc);
}
```

#### 3.3 Per DATABASE_SCHEMA.md:

```javascript
// Rileva modifiche schema
const schemaChanges = detectSchemaChanges();

if (schemaChanges.migrations) {
  appendSection(
    "DATABASE_SCHEMA.md",
    "## Migrazioni Recenti",
    schemaChanges.migrations
  );
}

if (schemaChanges.newModels) {
  updateModelsSection("DATABASE_SCHEMA.md", schemaChanges.newModels);
}
```

### Fase 4: Smart Merge Strategy

#### Preservazione Contenuto Custom:

```markdown
<!-- TODUBA:START:AUTO -->

[Contenuto generato automaticamente]

<!-- TODUBA:END:AUTO -->

<!-- TODUBA:CUSTOM:START -->

[Contenuto custom preservato durante update]

<!-- TODUBA:CUSTOM:END -->
```

#### Conflict Resolution:

1. Preserva sempre sezioni custom
2. Se conflitto in auto-generated:
   - Backup versione vecchia
   - Genera nuova
   - Marca conflitti per review

### Fase 5: Validazione e Report

#### Se `--check`:

```
🔍 Toduba Update Docs - Analisi Cambiamenti

📊 Sommario:
- Commits dall'ultimo update: 15
- File modificati: 23
- Categorie impattate: API, Frontend, Tests

📝 Documenti che verrebbero aggiornati:
✓ INDEX.md (sempre aggiornato)
✓ API_ENDPOINTS.md (8 endpoint modificati)
✓ COMPONENTS.md (3 nuovi componenti)
✓ TESTING.md (nuovi test aggiunti)
○ DATABASE_SCHEMA.md (nessun cambiamento)
○ ARCHITECTURE.md (nessun cambiamento strutturale)

⏱️ Tempo stimato: ~8 secondi

Esegui senza --check per applicare gli aggiornamenti.
```

#### Update Effettivo:

```
🔄 Toduba Update Docs - Aggiornamento in Corso...

[===========----------] 55% Analizzando API changes...
[==================---] 85% Aggiornando COMPONENTS.md...
[====================] 100% Completato!

✅ Documentazione Aggiornata con Successo!

📊 Riepilogo Update:
- File analizzati: 23
- Documenti aggiornati: 4/10
- Tempo impiegato: 7.3s
- Risparmio vs regenerazione: ~45s

📝 Modifiche applicate:
✓ INDEX.md - Statistiche aggiornate
✓ API_ENDPOINTS.md - 8 endpoint aggiornati, 2 nuovi
✓ COMPONENTS.md - 3 nuovi componenti documentati
✓ TESTING.md - Coverage aggiornato al 87%

💾 metadata.json aggiornato:
- last_updated: 2024-10-31T15:30:00Z
- commits_since_generation: 0
- git_info.last_commit: abc123def

💡 Tip: Usa --check la prossima volta per preview
```

### Fase 6: Auto-Invocazione da Orchestrator

Quando chiamato automaticamente:

1. Sempre modalità silenziosa (no verbose)
2. Log minimo solo se errori
3. Return status code per orchestrator
4. Se fallisce, non bloccare task principale

## Ottimizzazioni Performance

1. **Caching**: Cache analisi file per 5 minuti
2. **Parallel Processing**: Analizza categorie in parallelo
3. **Incremental Parsing**: Parse solo diff, non file interi
4. **Smart Skip**: Skip file non documentabili (.test, .spec)
5. **Batch Updates**: Accumula modifiche, scrivi una volta

## Smart Incremental Updates (H1)

### Cache System

```typescript
class DocumentationCache {
  private cache = new Map();
  private maxAge = 5 * 60 * 1000; // 5 minuti

  async getAnalysis(filePath: string): Promise<Analysis | null> {
    const cached = this.cache.get(filePath);
    if (cached && Date.now() - cached.timestamp < this.maxAge) {
      return cached.analysis;
    }
    return null;
  }

  setAnalysis(filePath: string, analysis: Analysis) {
    this.cache.set(filePath, {
      analysis,
      timestamp: Date.now(),
      hash: this.calculateHash(filePath),
    });
  }

  async isValid(filePath: string): Promise<boolean> {
    const cached = this.cache.get(filePath);
    if (!cached) return false;

    const currentHash = await this.calculateHash(filePath);
    return cached.hash === currentHash;
  }
}
```

### Smart Change Detection

```javascript
// Usa git diff con analisi semantica
const detectSmartChanges = async () => {
  const changes = {
    breaking: [],
    feature: [],
    bugfix: [],
    refactor: [],
    documentation: [],
  };

  // Analizza AST per determinare tipo di cambiamento
  const diff = await git.diff("--cached", "--name-status");

  for (const file of diff) {
    const analysis = await analyzeFileChange(file);

    // Categorizza in base al contenuto, non solo al path
    if (analysis.breaksAPI) changes.breaking.push(file);
    else if (analysis.addsFeature) changes.feature.push(file);
    else if (analysis.fixesBug) changes.bugfix.push(file);
    else if (analysis.refactors) changes.refactor.push(file);
    else changes.documentation.push(file);
  }

  return changes;
};
```

### Dependency Graph Updates

```typescript
// Aggiorna solo documenti dipendenti
const updateDependentDocs = async (changedFile: string) => {
  const dependencyGraph = await loadDependencyGraph();
  const affected = dependencyGraph.getDependents(changedFile);

  // Update solo documenti realmente impattati
  for (const doc of affected) {
    await updateSection(doc, changedFile);
  }
};
```

## Multiple Export Formats (H2)

### Format Converters

```typescript
interface FormatConverter {
  convert(markdown: string, options?: any): string | Buffer;
  extension: string;
  mimeType: string;
}

const converters: Record<string, FormatConverter> = {
  html: {
    convert: (md) => {
      const html = marked.parse(md);
      return `
<!DOCTYPE html>
<html>
<head>
  <title>Toduba Documentation</title>
  <link rel="stylesheet" href="toduba-docs.css">
</head>
<body>${html}</body>
</html>`;
    },
    extension: ".html",
    mimeType: "text/html",
  },

  json: {
    convert: (md) => {
      const sections = parseMarkdownToSections(md);
      return JSON.stringify(
        {
          version: "2.0.0",
          generated: new Date().toISOString(),
          sections,
          metadata: getMetadata(),
        },
        null,
        2
      );
    },
    extension: ".json",
    mimeType: "application/json",
  },

  pdf: {
    convert: async (md) => {
      // Usa markdown-pdf o puppeteer
      const html = marked.parse(md);
      return await generatePDF(html);
    },
    extension: ".pdf",
    mimeType: "application/pdf",
  },
};
```

### Export Pipeline

```javascript
const exportDocumentation = async (format: string = "md") => {
  const converter = converters[format];
  if (!converter) throw new Error(`Format ${format} not supported`);

  // Crea directory per formato
  const outputDir = `docs/export/${format}`;
  await fs.mkdir(outputDir, { recursive: true });

  // Converti tutti i documenti
  for (const file of await glob("docs/*.md")) {
    const content = await fs.readFile(file, "utf8");
    const converted = await converter.convert(content);

    const outputName = path.basename(file, ".md") + converter.extension;
    await fs.writeFile(`${outputDir}/${outputName}`, converted);
  }

  console.log(`✅ Exported to ${outputDir}/`);
};
```

### Format-Specific Templates

```typescript
// Templates per diversi formati
const templates = {
  html: {
    css: `
      .toduba-docs {
        font-family: 'Inter', sans-serif;
        max-width: 1200px;
        margin: 0 auto;
      }
      .sidebar { position: fixed; left: 0; width: 250px; }
      .content { margin-left: 270px; }
      .code-block { background: #f4f4f4; padding: 1rem; }
    `,
  },

  pdf: {
    pageSize: "A4",
    margins: { top: "2cm", bottom: "2cm", left: "2cm", right: "2cm" },
    header: "🤖 Toduba Documentation",
    footer: "Page {page} of {pages}",
  },
};
```

## Gestione Errori

- **Metadata corrotto**: Fallback a rigenerazione
- **Git history perso**: Usa timestamp per determinare modifiche
- **Conflitti merge**: Crea .backup e procedi
- **Docs readonly**: Alert user, skip update
- **Out of sync**: Se > 100 commits, suggerisci --full
- **Cache invalido**: Invalida e rigenera
- **Export fallito**: Mantieni formato originale

## Performance Metrics

```
📊 Smart Update Performance:
- Cache hit rate: 75%
- Average update time: 3.2s (vs 45s full)
- Memory usage: -60% con streaming
- File I/O: -80% con cache
```

## Integrazione con Orchestrator

L'orchestrator invoca automaticamente quando:

```javascript
if (modifiedFiles > 10 || majorRefactoring || newModules) {
  await invokeCommand("toduba-update-docs --smart");
}
```

Non viene invocato per:

- Modifiche a singoli file
- Fix di typo/commenti
- Modifiche solo a test
- Operazioni di configurazione

## Output con Multiple Formats

```
🔄 Toduba Smart Update - Multiple Formats

📊 Analisi Smart:
- Cache hits: 18/23 (78%)
- Semantic changes detected: 5
- Affected documents: 3

📝 Generazione formati:
[====] MD: ✅ 100% (base format)
[====] HTML: ✅ 100% (con styling)
[====] JSON: ✅ 100% (structured data)
[====] PDF: ✅ 100% (print-ready)

✅ Export completato:
- docs/export/html/
- docs/export/json/
- docs/export/pdf/

⚡ Performance:
- Tempo totale: 4.8s
- Risparmio: 89% vs full regeneration
```
