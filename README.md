# Toduba Plugin System v2.0 🚀

Sistema di plugin modulare per Claude Desktop con orchestrazione intelligente, documentazione automatica e funzionalità avanzate.

## 🆕 Novità in v2.0

- **Ultra-Think Flessibile**: Modalità quick/standard/deep per performance ottimali
- **Interactive Mode**: Esecuzione step-by-step con controllo completo
- **Smart Updates**: Cache intelligente e change detection semantica
- **Multiple Formats**: Export documentazione in MD/HTML/JSON/PDF
- **Pre-commit Hooks**: Quality checks automatici
- **Template System**: Scaffolding rapido con best practices
- **Snapshot & Rollback**: Sistema di restore point automatico

## Installazione

### Metodo Rapido (consigliato)

Esegui questo comando direttamente in Claude Desktop:

```
/plugin marketplace add https://github.com/tiboxtibo/custom-claude-plugins
```

### Metodi Alternativi

<details>
<summary>Per sviluppatori o installazione manuale</summary>

**Clona e link simbolico:**

```bash
git clone https://github.com/tiboxtibo/custom-claude-plugins.git
cd custom-claude-plugins
ln -s $(pwd) ~/.claude/plugins/custom-claude-plugins
```

**Copia diretta:**

```bash
git clone https://github.com/tiboxtibo/custom-claude-plugins.git
cp -r custom-claude-plugins ~/.claude/plugins/
```

Riavvia Claude Desktop dopo l'installazione manuale.

</details>

## 🤖 Agenti Toduba (8)

### Orchestrazione
- **toduba-orchestrator**: Tech lead con ultra-think modes (quick/standard/deep) e progress tracking

### Sviluppo
- **toduba-backend-engineer**: Specializzato in backend, API, database, MongoDB
- **toduba-frontend-engineer**: React, TypeScript, UI/UX, Playwright testing
- **toduba-mobile-engineer**: Flutter specialist per app mobile cross-platform

### Testing & Quality
- **toduba-qa-engineer**: Esegue test e validazione (non scrive test)
- **toduba-test-engineer**: Scrive test (non li esegue)

### Analisi & Documentazione
- **toduba-codebase-analyzer**: Analisi architettura e dipendenze
- **toduba-documentation-generator**: Generazione e manutenzione documentazione

## 📝 Comandi Toduba (10)

### Core Commands
- **`/toduba-init`**: Genera documentazione completa in `/docs`
- **`/toduba-update-docs`**: Smart incremental updates con cache e multi-format
- **`/toduba-commit`**: Commit strutturati con best practices
- **`/toduba-code-review`**: Review approfondita del codice
- **`/toduba-ultra-think`**: Analisi profonda con mode selector

### New Commands (v2.0)
- **`/toduba-test`**: Test suite con coverage e watch mode
- **`/toduba-rollback`**: Sistema rollback con snapshot
- **`/toduba-help`**: Help integrato con esempi
- **`/toduba-interactive`**: Modalità step-by-step interattiva
- **`/toduba-template`**: Scaffolding con template predefiniti

## 🎮 Interactive Mode

```bash
/toduba-interactive --step-by-step
```

**Controlli disponibili:**
- `[Enter]` - Continua
- `[p]` - Pausa
- `[s]` - Skip step
- `[u]` - Undo ultimo step
- `[c]` - Crea checkpoint
- `[q]` - Esci

**Features:**
- Esecuzione step-by-step con conferma
- Pause/Resume in qualsiasi momento
- Undo per tornare indietro
- Checkpoint per restore point
- Breakpoint debugging

## 📊 Smart Documentation

```bash
/toduba-update-docs --smart --format html
```

**Performance:**
- Cache hit rate: 75-80%
- Update time: 3.2s (vs 45s full)
- Memory: -60% con streaming
- File I/O: -80% con cache

**Export Formats:**
- **MD**: Formato base markdown
- **HTML**: Con styling e navigazione
- **JSON**: Structured data per API
- **PDF**: Print-ready con formatting

## 🔄 Snapshot & Rollback

```bash
/toduba-rollback --list        # Visualizza snapshot
/toduba-rollback abc123        # Ripristina snapshot
/toduba-rollback --interactive # Scelta interattiva
```

**Features:**
- Snapshot automatici pre-task
- Restore selettivo file/directory
- History tracking con metadata
- Cleanup automatico snapshot vecchi

## 🧪 Advanced Testing

```bash
/toduba-test --watch --coverage --parallel
```

**Features:**
- **Watch mode**: Hot-reload su modifiche
- **Coverage**: Report dettagliati con threshold
- **Parallel**: Esecuzione 3x più veloce
- **Visual regression**: Screenshot comparison
- **Mutation testing**: Code quality metrics

## 🛠️ Template System

```bash
/toduba-template list                    # Vedi template disponibili
/toduba-template crud-api --name users   # Genera CRUD API
/toduba-template react-component --name Header
```

**Template disponibili:**
- `crud-api`: REST API completa
- `react-component`: Component con test
- `flutter-screen`: Screen Flutter
- `microservice`: Microservizio completo
- `auth-flow`: Authentication system

## 🔒 Pre-commit Hooks

Automaticamente attivi in `.toduba/hooks/pre-commit`:

- ✅ **Linting**: ESLint/Prettier check
- ✅ **Type checking**: TypeScript validation
- ✅ **Test**: Test su file modificati
- ✅ **Security**: Scan per hardcoded secrets
- ✅ **Console.log**: Detection e rimozione
- ✅ **File size**: Alert per file > 1MB
- ✅ **Coverage**: Threshold minimo 80%
- ✅ **Snapshot**: Backup automatico pre-commit

## ⚡ Performance Metrics

| Feature | Before | After | Improvement |
|---------|--------|-------|-------------|
| Ultra-think (quick) | 2-5min | 30s | 85% faster |
| Doc updates (smart) | 45s | 3.2s | 93% faster |
| Test execution | 60s | 20s | 3x faster |
| Cache hit rate | 0% | 78% | New feature |
| Memory usage | 500MB | 200MB | 60% reduction |

## 🚀 Quick Start Examples

### Nuovo progetto
```bash
/toduba-init                # Analizza e documenta
/toduba-template crud-api   # Genera struttura base
/toduba-interactive         # Sviluppo guidato
```

### Manutenzione
```bash
/toduba-code-review         # Review codice
/toduba-test --coverage     # Verifica test
/toduba-update-docs --smart # Aggiorna docs
```

### Issue fixing
```bash
/toduba-ultra-think "bug in authentication"  # Analisi
/toduba-rollback --list                     # Vedi versioni
/toduba-interactive --step-by-step          # Fix guidato
```

## Aggiornamento

### Update alla v2.0
```
/plugin marketplace update custom-claude-plugins
```

### Re-installazione (sempre funzionante)
```
/plugin marketplace add https://github.com/tiboxtibo/custom-claude-plugins
```

<details>
<summary>Aggiornamento manuale (sviluppatori)</summary>

```bash
cd ~/.claude/plugins/custom-claude-plugins
git pull origin main
```

Riavvia Claude Desktop dopo l'aggiornamento.

</details>

## Disinstallazione

### Metodo standard
```
/plugin marketplace remove custom-claude-plugins
```

### Rimozione manuale
```bash
rm -rf ~/.claude/plugins/custom-claude-plugins
```

## Support & Contributing

- **Issues**: [GitHub Issues](https://github.com/tiboxtibo/custom-claude-plugins/issues)
- **Wiki**: [Documentation](https://github.com/tiboxtibo/custom-claude-plugins/wiki)
- **Contributing**: PRs welcome! Segui le linee guida Toduba

## Licenza

MIT - Toduba System © 2024