# Toduba Documentation System V2.0 - Guida Utente 📚

> Sistema di documentazione gerarchica con auto-detection e template intelligenti

## 🎯 Cosa è cambiato nella V2.0

### Nuova Struttura Gerarchica

La documentazione ora è organizzata in **tre livelli**:

```
docs/
├── global/          # Documentazione globale progetto
├── services/        # Documentazione per-servizio (sempre presente)
└── operations/      # Documentazione DevOps
```

### Supporto Monorepo Nativo

Il sistema rileva automaticamente se il progetto è:
- **Single Service**: Un solo servizio → `docs/services/[nome-progetto]/`
- **Monorepo**: Più servizi → `docs/services/[app|frontend|backend|...]/`

### Template Semi-Dinamici

I template ora sono **intelligenti**:
- Placeholder popolati automaticamente dall'analisi del codebase
- Sezioni TODO per completamento manuale
- Generazione condizionale (es: ENDPOINTS.md solo per backend)

## 🚀 Quick Start

### 1. Genera Documentazione Iniziale

```bash
/toduba-system:toduba-init
```

Questo comando:
- ✅ Rileva automaticamente il tipo di progetto
- ✅ Identifica i servizi presenti
- ✅ Analizza tech stack per ogni servizio
- ✅ Genera documentazione completa con placeholder intelligenti
- ✅ Crea metadata per update futuri

**Output**:
```
docs/
├── .toduba-meta/
│   ├── project-type.json
│   ├── services.json
│   ├── last-update.json
│   └── service_*.json
├── global/
│   ├── README.md
│   ├── ARCHITECTURE.md
│   ├── CONTRIBUTING.md
│   └── adr/
├── services/
│   └── [tuo-servizio]/
│       ├── README.md (Tier 1)
│       ├── SETUP.md (Tier 1)
│       ├── ARCHITECTURE.md (Tier 1)
│       ├── TECH-STACK.md (Tier 1)
│       ├── STYLE-GUIDE.md (Tier 1)
│       ├── ENDPOINTS.md (Tier 2 - se backend)
│       ├── DATABASE.md (Tier 2 - se ha DB)
│       ├── TESTING.md (Tier 2)
│       └── TROUBLESHOOTING.md (Tier 2)
└── operations/
    ├── DEPLOYMENT.md
    ├── CI-CD.md
    ├── MONITORING.md
    ├── SECURITY.md
    └── ENVIRONMENT-VARS.md
```

### 2. Completa i Placeholder

I file generati contengono:
- ✅ Informazioni auto-populate (nome progetto, tech stack, stats)
- 📝 Sezioni TODO da completare manualmente

**Esempio**:
```markdown
## Tech Stack

**Primary Language**: TypeScript
**Framework**: Express.js
**Lines of Code**: 15420

<!-- TODO: Descrivere perché è stato scelto Express.js -->
```

**Cerca nel codice**:
```bash
# Trova tutti i TODO
grep -r "TODO" docs/
```

### 3. Aggiornamenti Futuri

```bash
# Update incrementale (veloce)
/toduba-system:toduba-update-docs

# Update solo un servizio specifico
/toduba-system:toduba-update-docs --service backend

# Preview cosa verrà aggiornato
/toduba-system:toduba-update-docs --check

# Rigenerazione completa (se necessario)
/toduba-system:toduba-init --force
```

## 📂 Struttura Dettagliata

### Global Documentation

**Quando usare**: Informazioni che riguardano l'intero progetto

| File | Contenuto | Quando completare |
|------|-----------|-------------------|
| `README.md` | Overview generale del progetto | Subito |
| `ARCHITECTURE.md` | Architettura sistema completo | Dopo design architetturale |
| `SETUP.md` | Setup globale (monorepo) | Se è monorepo |
| `CONTRIBUTING.md` | Linee guida contribuzione | Prima di aprire a contributori |
| `adr/` | Architecture Decision Records | Quando prendi decisioni architetturali |

### Service Documentation (Tier 1 - SEMPRE)

**Quando usare**: Documentazione specifica di un servizio

| File | Contenuto | Chi lo completa |
|------|-----------|-----------------|
| `README.md` | Overview servizio | Dev lead |
| `SETUP.md` | Setup specifico servizio | Dev lead + New joiners feedback |
| `ARCHITECTURE.md` | Architettura interna servizio | Architects |
| `TECH-STACK.md` | Tecnologie, librerie, perché scelte | Tech leads |
| `STYLE-GUIDE.md` | Convenzioni codice | Team (consensus) |

### Service Documentation (Tier 2 - CONDIZIONALE)

**Quando usare**: Documentazione tecnica avanzata

| File | Quando generato | Chi lo completa |
|------|----------------|-----------------|
| `ENDPOINTS.md` | Solo backend/API | Backend devs |
| `DATABASE.md` | Solo se ha database | DB architects + Backend devs |
| `TESTING.md` | Sempre | QA + Devs |
| `TROUBLESHOOTING.md` | Sempre | Support team + Devs (dopo incident) |

### Operations Documentation

**Quando usare**: Informazioni DevOps e infrastruttura

| File | Contenuto | Chi lo completa |
|------|-----------|-----------------|
| `DEPLOYMENT.md` | Procedure deployment | DevOps team |
| `CI-CD.md` | Pipeline configurazione | DevOps + Lead devs |
| `MONITORING.md` | Logging, metrics, alerts | DevOps + SRE |
| `SECURITY.md` | Security best practices | Security team + DevOps |
| `ENVIRONMENT-VARS.md` | Config env vars | DevOps + Devs |

## 🔍 Auto-Detection

### Tipo Progetto

Il sistema rileva automaticamente:

| Indicatore | Tipo Rilevato |
|-----------|---------------|
| `pnpm-workspace.yaml` | Monorepo |
| `lerna.json` | Monorepo |
| `nx.json` | Monorepo |
| `package.json` con `"workspaces"` | Monorepo |
| > 1 `package.json` in subdirectories | Monorepo |
| Singolo `package.json` | Single Service |

### Tipo Servizio

| Dependencies | Tipo Servizio |
|-------------|---------------|
| express, fastify, @nestjs/core | Backend |
| react, vue, angular | Frontend |
| react-native | Mobile |
| pubspec.yaml (Flutter) | Mobile |
| fastapi, flask, django (Python) | Backend |
| go.mod | Backend |

### Rilevamento Database

| Indicatore | Database |
|-----------|----------|
| prisma | PostgreSQL/MySQL/SQLite |
| sequelize | PostgreSQL/MySQL |
| mongoose | MongoDB |
| typeorm | Multi-DB |

## 💡 Best Practices

### 1. Completa i TODO Gradualmente

**Non serve completare tutto subito!**

**Priorità**:
1. 🔥 **Alta**: README.md, SETUP.md (necessari per onboarding)
2. ⭐ **Media**: ARCHITECTURE.md, TECH-STACK.md (importanti per team growth)
3. 📝 **Bassa**: TROUBLESHOOTING.md (completa dopo che emerge)

### 2. Usa ADR per Decisioni Importanti

**Quando creare una ADR**:
- ✅ Scelta tra framework diversi
- ✅ Cambio pattern architetturale
- ✅ Adozione nuova tecnologia
- ✅ Decisione che impatta long-term

**Come crearla**:
```bash
# Copia template
cp docs/global/adr/0001-template.md docs/global/adr/0002-use-postgresql.md

# Compila tutte le sezioni
# Commit e crea PR per review team
```

### 3. Mantieni Aggiornato

```bash
# Dopo ogni feature importante
/toduba-system:toduba-update-docs

# Update specifico dopo modifica backend
/toduba-system:toduba-update-docs --service backend

# Verifica cosa cambierebbe senza modificare
/toduba-system:toduba-update-docs --check
```

### 4. Integra nel Workflow

**Pre-commit hook** (opzionale):
```bash
# .toduba/hooks/pre-commit
#!/bin/bash
# Update automatico docs prima di commit
/toduba-system:toduba-update-docs --smart
```

**CI/CD** (raccomandato):
```yaml
# .github/workflows/docs.yml
on:
  push:
    branches: [main]

jobs:
  update-docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Update documentation
        run: /toduba-system:toduba-update-docs
      - name: Commit changes
        run: |
          git config user.name "Toduba Bot"
          git add docs/
          git commit -m "docs: auto-update documentation"
          git push
```

## 🎨 Personalizzazione Template

### Modifica Template Esistenti

I template si trovano in:
```
templates/docs/
├── tier1/
│   ├── README.template.md
│   ├── ARCHITECTURE.template.md
│   ├── SETUP.template.md
│   ├── TECH-STACK.template.md
│   ├── STYLE-GUIDE.template.md
│   ├── CONTRIBUTING.template.md
│   └── ADR-TEMPLATE.template.md
└── tier2/
    ├── ENDPOINTS.template.md
    ├── DATABASE.template.md
    ├── TESTING.template.md
    └── TROUBLESHOOTING.template.md
```

**Placeholder disponibili**:
- `{{PROJECT_NAME}}` - Nome progetto
- `{{SERVICE_NAME}}` - Nome servizio
- `{{PRIMARY_LANGUAGE}}` - Linguaggio principale
- `{{PRIMARY_FRAMEWORK}}` - Framework principale
- `{{TIMESTAMP}}` - Data generazione
- `{{TODUBA_VERSION}}` - Versione Toduba
- E molti altri (vedi template)

### Aggiungi Template Custom

1. Crea nuovo template in `templates/docs/tier1/` o `tier2/`
2. Usa placeholder `{{VAR_NAME}}`
3. Modifica `commands/toduba-init.md` per includerlo

## 🐛 Troubleshooting

### Problema: "Documentazione V1.0 rilevata"

**Causa**: Hai ancora vecchia struttura docs flat

**Soluzione**:
```bash
# Backup automatico + upgrade a V2.0
/toduba-system:toduba-init --force
```

### Problema: Servizio non rilevato

**Causa**: Struttura non standard

**Soluzione**: Il sistema usa root come fallback. Modifica manualmente:
```bash
# 1. Aggiungi servizio a docs/.toduba-meta/services.json
# 2. Crea directory docs/services/[nome]/
# 3. Rigenera
/toduba-system:toduba-init --force
```

### Problema: Template con troppi TODO

**Causa**: Auto-detection non ha trovato alcune info

**Soluzione**:
1. Questo è normale! I TODO sono intenzionali
2. Completa gradualmente (vedi Best Practices)
3. Alcuni placeholder non possono essere auto-popolati (es: "perché abbiamo scelto X")

## 📊 Metadata System

### File Metadata

```
docs/.toduba-meta/
├── project-type.json      # Tipo progetto (monorepo/single)
├── services.json          # Lista servizi
├── last-update.json       # Info ultimo update
└── service_*.json         # Metadata per servizio
```

**Esempio `service_backend.json`**:
```json
{
  "name": "backend",
  "path": "./backend",
  "type": "backend",
  "primary_language": "typescript",
  "primary_framework": "express",
  "has_database": true,
  "database_type": "postgresql",
  "test_framework": "jest",
  "file_count": 145,
  "loc_count": 15420,
  "analyzed_at": "2024-01-01T00:00:00Z"
}
```

### Usi dei Metadata

- ✅ Update incrementali veloci
- ✅ Tracking modifiche per servizio
- ✅ Cache analisi codebase
- ✅ Statistiche progetto

## 🎯 Casi d'Uso Comuni

### Caso 1: Nuovo Progetto

```bash
# 1. Init progetto
npm init
# ... setup progetto ...

# 2. Genera documentazione
/toduba-system:toduba-init

# 3. Completa README e SETUP per onboarding
# 4. Commit docs/
git add docs/
git commit -m "docs: initial documentation"
```

### Caso 2: Progetto Esistente Senza Docs

```bash
# 1. Genera documentazione iniziale
/toduba-system:toduba-init

# 2. Review e completa gradualmente
# 3. Integra nel workflow team
```

### Caso 3: Monorepo con Più Servizi

```bash
# 1. Init (rileva automaticamente tutti i servizi)
/toduba-system:toduba-init

# Output:
# docs/
# ├── global/           # Overview monorepo
# ├── services/
# │   ├── frontend/     # Docs frontend
# │   ├── backend/      # Docs backend
# │   └── mobile/       # Docs mobile app
# └── operations/       # DevOps condiviso

# 2. Ogni team completa la propria cartella
# 3. Global docs per architettura generale
```

### Caso 4: Update Dopo Feature

```bash
# 1. Sviluppi feature nel backend
# 2. Update incrementale
/toduba-system:toduba-update-docs --service backend

# 3. Verifica cambiamenti
/toduba-system:toduba-update-docs --check

# 4. Commit
git add docs/services/backend/
git commit -m "docs: update backend documentation after feature X"
```

## 📚 Risorse Aggiuntive

### Documentazione Comandi

- `/toduba-system:toduba-init` - Generazione iniziale
- `/toduba-system:toduba-update-docs` - Update incrementale
- `/toduba-system:toduba-help` - Help system integrato

### Template Examples

Vedi `templates/docs/` per tutti i template disponibili

### Metadata Schema

Vedi `docs/.toduba-meta/` dopo prima generazione

---

## 🎉 Conclusione

Il **Toduba Documentation System V2.0** offre:

✅ **Auto-detection intelligente** - Rileva automaticamente progetto e servizi
✅ **Struttura gerarchica** - Organizzazione chiara e scalabile
✅ **Template semi-dinamici** - Placeholder intelligenti + TODO manuali
✅ **Update incrementali** - Veloci e smart
✅ **Tier-based docs** - Tier 1 (sempre) + Tier 2 (condizionale)
✅ **Monorepo native** - Supporto monorepo first-class
✅ **Metadata tracking** - Sistema di cache e tracking avanzato

**Inizia ora**:
```bash
/toduba-system:toduba-init
```

---
*Toduba Documentation System V2.0 - Generated with ❤️ by Toduba Team*
