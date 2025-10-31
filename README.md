# Custom Claude Plugins

Collezione di plugin personalizzati per Claude Desktop: agent specializzati, comandi e skill per sviluppo software.

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

## Utilizzo

### Agent Disponibili

- **tech-lead-software-architect**: Orchestrazione progetti e coordinamento team
- **frontend-engineer**: Sviluppo React/TypeScript e UI/UX
- **backend-engineer**: Architettura NextJS/MongoDB
- **ai-engineer**: Sistemi AI, LLM e prompt engineering
- **qa-engineer**: Testing automatizzato e quality assurance
- **debug-detective**: Risoluzione bug complessi e debugging
- **code-reviewer**: Review codice e best practices
- **cybersec-engineer**: Analisi sicurezza e vulnerability assessment
- **frontend-issue-fixer**: Risoluzione issue UI con Playwright
- **backend-issue-fixer**: Fix issue backend e API
- **pr-diff-documenter**: Documentazione differenze PR
- **playwright-issue-analyzer**: Analisi issue con Playwright
- **documentation-generator**: Generazione documentazione automatica
- **codebase-analyzer**: Analisi profonda codebase per knowledge base

### Comandi

#### Comandi Principali

- `/init`: Analizza codebase e genera knowledge base in .claude-docs/ (da eseguire per primo)
- `/ultra-think [problema]`: Analisi approfondita e problem solving
- `/fix-issue [nome] [--type]`: Risolve issue con agenti specializzati
- `/fix-frontend-issue [file/nome]`: Fix issue frontend con validazione
- `/sonarqube-fix`: Risolve issue SonarQube per PR corrente
- `/migrate-to-k8s [app]`: Migrazione Kubernetes con FluxCD e Istio

#### Gestione PR e Git

- `/gh-review-pr`: Review dettagliata di pull request
- `/gh-commit`: Crea commit con messaggio strutturato
- `/gh-fix-ci`: Risolve problemi CI/CD pipeline
- `/gh-address-pr-comments`: Gestisce commenti PR
- `/pr-review`: Analisi approfondita codice PR
- `/add-changelog`: Genera/aggiorna changelog

#### Documentazione

- `/docs [topic]`: Accede alla documentazione Claude Code
- `/generate-api-documentation`: Genera documentazione API
- `/create-architecture-documentation`: Crea diagrammi architettura
- `/create-onboarding-guide`: Genera guida onboarding
- `/report`: Genera report progetto

#### Testing e Qualità

- `/make-tests`: Genera test automatizzati
- `/de-slop`: Migliora qualità del codice
- `/security-audit`: Audit sicurezza completo
- `/performance-audit`: Analisi performance

#### Sviluppo

- `/explore-plan-code-test`: Workflow completo sviluppo
- `/implement-caching-strategy`: Implementa strategia cache
- `/add-performance-monitoring`: Aggiunge monitoring performance

### Skill

- **fairmind-context**: Raccolta contesto progetto Fairmind
- **fairmind-tdd**: Workflow TDD con acceptance criteria
- **fairmind-code-review**: Review sistematica con tracciabilità

## Quick Start per Nuovi Progetti

1. **Installa il plugin** (se non già fatto):
   ```
   /plugin marketplace add https://github.com/tiboxtibo/custom-claude-plugins
   ```

2. **Inizializza la codebase** (IMPORTANTE - esegui per primo):
   ```
   /init
   ```
   Questo comando analizza l'intera codebase e crea una knowledge base in `.claude-docs/` che velocizza tutte le operazioni successive di 5-10x.

3. **Usa i comandi specializzati** per il tuo workflow:
   - Sviluppo: `/explore-plan-code-test`
   - Debugging: `/fix-issue [nome]`
   - Review: `/gh-review-pr`
   - Testing: `/make-tests`

## Aggiornamento

Quando il repository GitHub viene aggiornato, puoi ottenere l'ultima versione in due modi:

### Metodo 1: Comando update (per plugin già installati)

```
/plugin marketplace update custom-claude-plugins
```

### Metodo 2: Re-installazione (funziona sempre)

```
/plugin marketplace add https://github.com/tiboxtibo/custom-claude-plugins
```

> **Nota:** Entrambi i metodi scaricano automaticamente l'ultima versione dal repository GitHub. Non è necessario disinstallare prima - l'aggiornamento sovrascrive la versione precedente.

<details>
<summary>Aggiornamento manuale (per sviluppatori)</summary>

```bash
cd ~/.claude/plugins/custom-claude-plugins
git pull origin main
```

Riavvia Claude Desktop dopo l'aggiornamento manuale.

</details>

## Disinstallazione

Se vuoi rimuovere il plugin, hai due opzioni:

### Metodo standard

```
/plugin marketplace remove custom-claude-plugins
```

### Rimozione manuale

Per rimuovere installazioni manuali:

```bash
rm -rf ~/.claude/plugins/custom-claude-plugins
```

> **Nota:** Riavvia Claude Desktop dopo la disinstallazione per applicare le modifiche.

## Configurazione (Opzionale)

1. Copia il template di configurazione:

```bash
cp config.template.json config.json
```

2. Modifica `config.json` con le tue preferenze

## Struttura

```
├── agents/          # Agent specializzati
├── commands/        # Comandi slash personalizzati
├── skills/          # Skill riutilizzabili
├── code-review/     # Workflow code review
├── context-gather/  # Raccolta contesto
└── tdd-workflow/    # Workflow TDD
```

## Licenza

MIT
