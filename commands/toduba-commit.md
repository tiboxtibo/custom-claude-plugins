---
allowed-tools:
  - Bash
  - Read
  - Grep
argument-hint: "[message]"
description: "Crea commit con messaggi strutturati seguendo best practices"
---

# Toduba Commit - Gestione Commit Strutturati 📝

## Obiettivo

Creare commit Git con messaggi ben strutturati, seguendo le convenzioni e best practices del progetto.

## Argomenti

- `message` (opzionale): Messaggio di commit personalizzato

Argomenti ricevuti: $ARGUMENTS

## Processo di Commit

### Fase 1: Analisi Modifiche

```bash
# Verifica stato repository
git status --porcelain

# Mostra diff delle modifiche
git diff --stat

# Conta file modificati
MODIFIED_FILES=$(git status --porcelain | wc -l)
```

### Fase 2: Categorizzazione Modifiche

Determina il tipo di commit:

- `feat`: Nuova funzionalità
- `fix`: Bug fix
- `docs`: Solo documentazione
- `style`: Formattazione, no logic changes
- `refactor`: Refactoring codice
- `test`: Aggiunta o modifica test
- `chore`: Manutenzione, dipendenze
- `perf`: Performance improvements

### Fase 3: Generazione Messaggio

#### Formato Conventional Commits:

```
<type>(<scope>): <description>

[body opzionale]

[footer opzionale]
```

#### Esempi:

```
feat(auth): add JWT token refresh capability

Implemented automatic token refresh when the access token expires.
Added refresh token storage and validation logic.

Closes #123
```

### Fase 4: Pre-Commit Checks

```bash
# Run linting
npm run lint

# Run tests
npm test

# Check for console.logs
if grep -r "console.log" src/; then
  echo "⚠️ Warning: console.log trovati nel codice"
fi

# Check for TODO comments
if grep -r "TODO" src/; then
  echo "📝 Reminder: TODO comments trovati"
fi
```

### Fase 5: Creazione Commit

```bash
# Stage modifiche appropriate
git add -A

# Crea commit con messaggio strutturato
git commit -m "$(cat <<EOF
$COMMIT_TYPE($COMMIT_SCOPE): $COMMIT_MESSAGE

$COMMIT_BODY

🤖 Generated with Toduba System
Co-Authored-By: Toduba <noreply@toduba.it>
EOF
)"
```

## Analisi Intelligente per Messaggio

```typescript
const generateCommitMessage = (changes) => {
  // Analizza file modificati
  const analysis = {
    hasNewFiles: changes.some((c) => c.status === "A"),
    hasDeletedFiles: changes.some((c) => c.status === "D"),
    hasModifiedFiles: changes.some((c) => c.status === "M"),
    mainlyFrontend:
      changes.filter((c) => c.path.includes("components")).length > 0,
    mainlyBackend: changes.filter((c) => c.path.includes("api")).length > 0,
    mainlyTests: changes.filter((c) => c.path.includes(".test.")).length > 0,
    mainlyDocs:
      changes.filter((c) => c.path.match(/\.(md|txt|doc)/)).length > 0,
  };

  // Determina tipo
  let type = "chore";
  if (analysis.hasNewFiles && !analysis.mainlyTests) type = "feat";
  if (analysis.mainlyTests) type = "test";
  if (analysis.mainlyDocs) type = "docs";

  // Determina scope
  let scope = "general";
  if (analysis.mainlyFrontend) scope = "ui";
  if (analysis.mainlyBackend) scope = "api";
  if (analysis.mainlyTests) scope = "test";

  // Genera descrizione
  const description = summarizeChanges(changes);

  return {
    type,
    scope,
    description,
  };
};
```

## Template Messaggi

### Feature

```
feat(module): add new feature description

- Implemented X functionality
- Added Y configuration
- Created Z component

Related to #ISSUE
```

### Bug Fix

```
fix(module): resolve issue with X

Fixed the bug where X was causing Y.
The issue was due to Z condition not being handled.

Fixes #ISSUE
```

### Refactoring

```
refactor(module): improve X structure

- Extracted common logic to utilities
- Reduced code duplication
- Improved readability

No functional changes.
```

## Output

```
🔍 Analisi modifiche in corso...

📊 Riepilogo modifiche:
- File modificati: 5
- Aggiunti: 2
- Modificati: 3
- Eliminati: 0

📝 Tipo di commit identificato: feat
📁 Scope: backend
📌 Descrizione suggerita: add user authentication endpoints

✅ Pre-commit checks:
- Linting: PASSED
- Tests: PASSED
- Build: PASSED

💬 Messaggio di commit:
────────────────────────────────
feat(backend): add user authentication endpoints

Implemented login, logout, and token refresh endpoints.
Added JWT validation middleware and session management.

🤖 Generated with Toduba System
Co-Authored-By: Toduba <noreply@toduba.it>
────────────────────────────────

📤 Commit creato con successo!
Hash: abc123def456
Branch: feature/auth
Files: 5 changed, 203 insertions(+), 10 deletions(-)

💡 Prossimo step: git push origin feature/auth
```

## Best Practices

1. Commit atomici (una feature per commit)
2. Messaggi descrittivi e chiari
3. Usare tempo presente imperativo
4. Limitare subject line a 50 caratteri
5. Body dettagliato per commit complessi
6. Referenziare issue quando applicabile
7. No commit di file generati/build
8. Verificare sempre prima di committare
