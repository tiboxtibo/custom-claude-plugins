---
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
argument-hint: "[--check] [--full] [--smart] [--format md|html|json|pdf]"
description: "Smart incremental updates con cache e multiple export formats"
model: haiku
---

# Toduba Update Docs - Smart Incremental Updates 🔄

## Obiettivo
Aggiornamento intelligente con cache, change detection avanzata e supporto per multiple export formats.

## Argomenti
- `--check`: Mostra cosa verrebbe aggiornato senza modificare
- `--full`: Forza rigenerazione completa (equivalente a toduba-init --force)
- `--smart`: Abilita cache e ottimizzazioni AI (default: on)
- `--format`: Formato export (md, html, json, pdf) - default: md

Argomenti ricevuti: $ARGUMENTS

## Pre-requisiti
```bash
# Verifica che /docs esista
if [ ! -d "docs" ]; then
  echo "❌ Errore: Documentazione non trovata!"
  echo "   Esegui prima: /toduba-init"
  exit 1
fi

# Verifica metadata.json
if [ ! -f "docs/metadata.json" ]; then
  echo "⚠️ metadata.json mancante - rigenerazione completa necessaria"
  # Fallback a toduba-init
fi
```

## Processo di Aggiornamento Intelligente

### Fase 1: Analisi Cambiamenti

#### 1.1 Lettura Stato Precedente
```javascript
// Leggi metadata.json
const metadata = JSON.parse(readFile('docs/metadata.json'));
const lastCommit = metadata.git_info.last_commit;
const lastUpdate = metadata.last_updated;
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

### Fase 2: Decisione Aggiornamento

#### Matrice di Update:
```
Cambiamenti rilevati → Documenti da aggiornare
─────────────────────────────────────────────
API changes         → API_ENDPOINTS.md
Frontend changes    → COMPONENTS.md
Database changes    → DATABASE_SCHEMA.md
Test changes        → TESTING.md
Config changes      → CONFIGURATION.md
Package changes     → DEPENDENCIES.md
Major refactoring   → ARCHITECTURE.md
Any changes         → INDEX.md, metadata.json
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
const modifiedControllers = getModifiedFiles('controllers');
const modifiedRoutes = getModifiedFiles('routes');

// Estrai endpoint esistenti
const existingEndpoints = parseExistingEndpoints('API_ENDPOINTS.md');

// Analizza nuovi/modificati
const updatedEndpoints = analyzeEndpoints(modifiedControllers, modifiedRoutes);

// Merge intelligente
const mergedEndpoints = mergeEndpoints(existingEndpoints, updatedEndpoints);

// Rigenera solo sezioni cambiate
updateSections('API_ENDPOINTS.md', mergedEndpoints);
```

#### 3.2 Per COMPONENTS.md:
```javascript
// Simile approccio per componenti UI
const modifiedComponents = getModifiedFiles(['components', 'pages']);

// Aggiorna solo componenti modificati
for (const component of modifiedComponents) {
  const componentDoc = generateComponentDoc(component);
  replaceSection('COMPONENTS.md', component.name, componentDoc);
}
```

#### 3.3 Per DATABASE_SCHEMA.md:
```javascript
// Rileva modifiche schema
const schemaChanges = detectSchemaChanges();

if (schemaChanges.migrations) {
  appendSection('DATABASE_SCHEMA.md', '## Migrazioni Recenti', schemaChanges.migrations);
}

if (schemaChanges.newModels) {
  updateModelsSection('DATABASE_SCHEMA.md', schemaChanges.newModels);
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
      hash: this.calculateHash(filePath)
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
    documentation: []
  };

  // Analizza AST per determinare tipo di cambiamento
  const diff = await git.diff('--cached', '--name-status');

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
    extension: '.html',
    mimeType: 'text/html'
  },

  json: {
    convert: (md) => {
      const sections = parseMarkdownToSections(md);
      return JSON.stringify({
        version: '2.0.0',
        generated: new Date().toISOString(),
        sections,
        metadata: getMetadata()
      }, null, 2);
    },
    extension: '.json',
    mimeType: 'application/json'
  },

  pdf: {
    convert: async (md) => {
      // Usa markdown-pdf o puppeteer
      const html = marked.parse(md);
      return await generatePDF(html);
    },
    extension: '.pdf',
    mimeType: 'application/pdf'
  }
};
```

### Export Pipeline
```javascript
const exportDocumentation = async (format: string = 'md') => {
  const converter = converters[format];
  if (!converter) throw new Error(`Format ${format} not supported`);

  // Crea directory per formato
  const outputDir = `docs/export/${format}`;
  await fs.mkdir(outputDir, { recursive: true });

  // Converti tutti i documenti
  for (const file of await glob('docs/*.md')) {
    const content = await fs.readFile(file, 'utf8');
    const converted = await converter.convert(content);

    const outputName = path.basename(file, '.md') + converter.extension;
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
    `
  },

  pdf: {
    pageSize: 'A4',
    margins: { top: '2cm', bottom: '2cm', left: '2cm', right: '2cm' },
    header: '🤖 Toduba Documentation',
    footer: 'Page {page} of {pages}'
  }
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
  await invokeCommand('toduba-update-docs --smart');
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