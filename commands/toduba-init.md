---
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
argument-hint: "[--force] [--verbose]"
description: "Analizza il progetto e genera documentazione completa in /docs"
model: sonnet
---

# Toduba Init - Generazione Documentazione Progetto 📚

## Obiettivo

Analizzare l'intero progetto e generare una documentazione completa nella cartella `/docs`. Se la documentazione esiste già, aggiorna solo le parti modificate.

## Argomenti

- `--force`: Rigenera completamente la documentazione anche se esiste
- `--verbose`: Output dettagliato durante la generazione

Argomenti ricevuti: $ARGUMENTS

## Processo di Analisi e Generazione

### Fase 1: Verifica Stato Attuale

```bash
# Controlla se /docs esiste
if [ -d "docs" ]; then
  # Controlla metadata.json per capire quanto è vecchia
  # Se --force, cancella tutto e ricomincia
  # Altrimenti, modalità incrementale
fi
```

### Fase 2: Analisi del Progetto

#### 2.1 Identificazione Tipo Progetto

Cerca indicatori chiave:

- `package.json` → Node.js/JavaScript/TypeScript
- `pubspec.yaml` → Flutter/Dart
- `pom.xml` / `build.gradle` → Java
- `requirements.txt` / `setup.py` → Python
- `Cargo.toml` → Rust
- `go.mod` → Go
- `.csproj` → C#/.NET

#### 2.2 Struttura del Progetto

```bash
# Genera albero delle directory (escludendo node_modules, .git, etc.)
tree -I 'node_modules|.git|dist|build|coverage' -d -L 3

# Conta file per tipo
find . -type f -name "*.js" | wc -l
find . -type f -name "*.ts" | wc -l
# etc...
```

#### 2.3 Analisi Componenti

**Frontend (se presente):**

- Framework utilizzato (React, Vue, Angular, Flutter)
- Componenti principali
- Routing structure
- State management
- Stili e temi

**Backend (se presente):**

- Framework (Express, FastAPI, Spring, etc.)
- API endpoints
- Middleware
- Database connections
- Authentication

**Database (se presente):**

- Tipo (SQL, NoSQL)
- Schema/Modelli
- Migrazioni
- Indici

### Fase 3: Generazione Documentazione

#### Struttura `/docs`:

```
docs/
├── INDEX.md                 # Overview del progetto
├── ARCHITECTURE.md          # Architettura sistema
├── API_ENDPOINTS.md         # Documentazione API (se backend)
├── DATABASE_SCHEMA.md       # Schema database (se presente)
├── COMPONENTS.md            # Componenti UI (se frontend)
├── MOBILE_STRUCTURE.md      # Struttura app (se mobile)
├── DEPENDENCIES.md          # Dipendenze e versioni
├── CONFIGURATION.md         # Configurazioni e environment
├── TESTING.md              # Strategia e copertura test
├── DEPLOYMENT.md           # Guide deployment
└── metadata.json           # Metadati generazione
```

#### Template INDEX.md:

```markdown
# [Nome Progetto] - Documentazione

Generato da Toduba System il [DATA]

## 📋 Overview

[Descrizione breve del progetto]

## 🏗️ Architettura

- **Tipo**: [Monolitico/Microservizi/Serverless]
- **Stack**: [Tecnologie principali]
- **Pattern**: [MVC/MVVM/Clean Architecture]

## 📁 Struttura Progetto

\`\`\`
[Tree output semplificato]
\`\`\`

## 🚀 Quick Start

1. [Passi installazione]
2. [Configurazione]
3. [Avvio]

## 📊 Statistiche Codebase

- **Linguaggi**: [Lista con percentuali]
- **File totali**: [Numero]
- **Linee di codice**: [Numero approssimativo]
- **Test coverage**: [Se disponibile]

## 🔗 Collegamenti Rapidi

- [Architettura](./ARCHITECTURE.md)
- [API](./API_ENDPOINTS.md)
- [Database](./DATABASE_SCHEMA.md)
- [Componenti](./COMPONENTS.md)
```

#### Template metadata.json:

```json
{
  "version": "1.0.0",
  "generated": "[ISO_DATE]",
  "last_updated": "[ISO_DATE]",
  "toduba_version": "2.0.0",
  "project_info": {
    "name": "[PROJECT_NAME]",
    "type": "[PROJECT_TYPE]",
    "main_language": "[LANGUAGE]",
    "frameworks": []
  },
  "analysis": {
    "files_analyzed": 0,
    "directories_scanned": 0,
    "time_taken_seconds": 0
  },
  "coverage": {
    "architecture": true,
    "api": false,
    "database": false,
    "components": false,
    "mobile": false,
    "testing": false
  },
  "git_info": {
    "last_commit": "[HASH]",
    "branch": "[BRANCH]",
    "commits_since_generation": 0
  }
}
```

### Fase 4: Gestione Aggiornamento Incrementale

Se la documentazione esiste e non viene usato `--force`:

1. **Leggi metadata.json esistente**
2. **Calcola differenze:**

   ```bash
   # Commits dal'ultima generazione
   git log --oneline [LAST_COMMIT]..HEAD

   # File modificati
   git diff --name-only [LAST_COMMIT]..HEAD
   ```

3. **Aggiorna solo sezioni necessarie:**
   - Se modificati file API → rigenera API_ENDPOINTS.md
   - Se modificati componenti → aggiorna COMPONENTS.md
   - Se modificato schema DB → aggiorna DATABASE_SCHEMA.md
   - Sempre aggiorna INDEX.md e metadata.json

### Fase 5: Output Finale

```
✅ Documentazione Toduba Generata con Successo!

📊 Riepilogo Analisi:
- Tipo progetto: [TIPO]
- File analizzati: [NUMERO]
- Documenti generati: [NUMERO]
- Tempo impiegato: [SECONDI]s

📁 Documentazione disponibile in: ./docs/

📝 File generati:
- INDEX.md (Overview progetto)
- ARCHITECTURE.md (Architettura sistema)
[lista altri file generati]

💡 Prossimi passi:
1. Revisiona la documentazione generata
2. Usa /toduba-update-docs per aggiornamenti futuri
3. Committa la cartella /docs nel repository

⚡ Performance tip: La prossima volta userò update incrementale
   (5-10x più veloce per aggiornamenti)
```

## Gestione Errori

- **Progetto non riconosciuto**: Genera documentazione base generica
- **Permessi insufficienti**: Suggerisci comandi con sudo
- **Git non inizializzato**: Procedi senza info git
- **Cartella docs protetta**: Chiedi conferma sovrascrittura

## Note Implementazione

- Usa `sonnet` model per velocità (analisi può essere estesa)
- Parallel processing dove possibile (analisi multi-file)
- Cache risultati intermedi in memoria
- Sanitizza output per evitare leak di secrets
- Escludi sempre: .env, .secrets, credentials, tokens
