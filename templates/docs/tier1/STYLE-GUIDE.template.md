# Style Guide - {{SERVICE_NAME}}

> 🎨 Linee guida e convenzioni per la scrittura del codice
> Ultimo aggiornamento: {{TIMESTAMP}}

## 📋 Overview

Questo documento definisce le convenzioni di codice, best practices e standard da seguire per mantenere il codice consistente, leggibile e manutenibile.

## 🎯 Principi Guida

1. **Leggibilità**: Il codice viene letto più spesso di quanto viene scritto
2. **Consistenza**: Seguire le stesse convenzioni in tutto il progetto
3. **Semplicità**: Preferire soluzioni semplici a soluzioni complesse
4. **Manutenibilità**: Scrivere codice facile da modificare e estendere

## 📝 Convenzioni Generali

### Naming Conventions

#### File e Directory

```
{{FILE_NAMING_CONVENTION}}
```

**Esempi**:
```
✅ Corretto:
{{CORRECT_FILENAME_EXAMPLES}}

❌ Sbagliato:
{{INCORRECT_FILENAME_EXAMPLES}}
```

#### Variabili

```{{PRIMARY_LANGUAGE}}
// {{VARIABLE_NAMING_STYLE}}
{{VARIABLE_EXAMPLES}}
```

**Regole**:
- {{VARIABLE_RULE_1}}
- {{VARIABLE_RULE_2}}
- {{VARIABLE_RULE_3}}

#### Funzioni/Metodi

```{{PRIMARY_LANGUAGE}}
// {{FUNCTION_NAMING_STYLE}}
{{FUNCTION_EXAMPLES}}
```

**Regole**:
- {{FUNCTION_RULE_1}}
- {{FUNCTION_RULE_2}}
- {{FUNCTION_RULE_3}}

#### Classi/Interfacce

```{{PRIMARY_LANGUAGE}}
// {{CLASS_NAMING_STYLE}}
{{CLASS_EXAMPLES}}
```

**Regole**:
- {{CLASS_RULE_1}}
- {{CLASS_RULE_2}}
- {{CLASS_RULE_3}}

#### Costanti

```{{PRIMARY_LANGUAGE}}
// {{CONSTANT_NAMING_STYLE}}
{{CONSTANT_EXAMPLES}}
```

### Indentazione e Formattazione

- **Indentazione**: {{INDENTATION_STYLE}}
- **Max line length**: {{MAX_LINE_LENGTH}} caratteri
- **Trailing commas**: {{TRAILING_COMMAS_RULE}}
- **Quotes**: {{QUOTE_STYLE}}

### Commenti

#### When to Comment

```{{PRIMARY_LANGUAGE}}
// ✅ CORRETTO: Spiega il PERCHÉ, non il COME
{{GOOD_COMMENT_EXAMPLE}}

// ❌ SBAGLIATO: Commento ridondante
{{BAD_COMMENT_EXAMPLE}}
```

#### Commenti di Documentazione

```{{PRIMARY_LANGUAGE}}
{{DOC_COMMENT_EXAMPLE}}
```

#### TODOs e FIXMEs

```{{PRIMARY_LANGUAGE}}
// TODO: {{TODO_FORMAT}}
// FIXME: {{FIXME_FORMAT}}
// HACK: {{HACK_FORMAT}}
// NOTE: {{NOTE_FORMAT}}
```

## 🏗️ Struttura del Codice

### Organizzazione File

```{{PRIMARY_LANGUAGE}}
{{FILE_ORGANIZATION_TEMPLATE}}
```

**Ordine standard**:
1. {{FILE_SECTION_1}}
2. {{FILE_SECTION_2}}
3. {{FILE_SECTION_3}}
4. {{FILE_SECTION_4}}

### Imports/Includes

```{{PRIMARY_LANGUAGE}}
// Ordine imports:
{{IMPORT_ORDER_EXAMPLE}}
```

**Regole**:
- {{IMPORT_RULE_1}}
- {{IMPORT_RULE_2}}
- {{IMPORT_RULE_3}}

## 🎯 Best Practices per {{PRIMARY_LANGUAGE}}

### Funzioni

**Lunghezza massima**: {{MAX_FUNCTION_LENGTH}} righe

**Parametri massimi**: {{MAX_PARAMETERS}}

**Principio**: Una funzione dovrebbe fare una sola cosa

```{{PRIMARY_LANGUAGE}}
// ✅ CORRETTO
{{GOOD_FUNCTION_EXAMPLE}}

// ❌ SBAGLIATO
{{BAD_FUNCTION_EXAMPLE}}
```

### Error Handling

```{{PRIMARY_LANGUAGE}}
{{ERROR_HANDLING_EXAMPLE}}
```

**Best practices**:
- {{ERROR_HANDLING_RULE_1}}
- {{ERROR_HANDLING_RULE_2}}
- {{ERROR_HANDLING_RULE_3}}

### Async/Await (se applicabile)

```{{PRIMARY_LANGUAGE}}
{{ASYNC_AWAIT_EXAMPLE}}
```

### Null Safety

```{{PRIMARY_LANGUAGE}}
{{NULL_SAFETY_EXAMPLE}}
```

## 🏛️ Architectural Patterns

### {{PATTERN_1_NAME}}

**Quando usarlo**: {{PATTERN_1_WHEN}}

```{{PRIMARY_LANGUAGE}}
{{PATTERN_1_EXAMPLE}}
```

### {{PATTERN_2_NAME}}

**Quando usarlo**: {{PATTERN_2_WHEN}}

```{{PRIMARY_LANGUAGE}}
{{PATTERN_2_EXAMPLE}}
```

## 🧪 Testing Conventions

### File di Test

**Naming**: {{TEST_FILE_NAMING}}

**Organizzazione**:
```
{{TEST_ORGANIZATION}}
```

### Struttura Test

```{{PRIMARY_LANGUAGE}}
{{TEST_STRUCTURE_EXAMPLE}}
```

### Best Practices Testing

- {{TEST_PRACTICE_1}}
- {{TEST_PRACTICE_2}}
- {{TEST_PRACTICE_3}}

## 🗄️ Database Conventions (se applicabile)

### Naming Tables

{{DB_TABLE_NAMING}}

### Naming Columns

{{DB_COLUMN_NAMING}}

### Migrations

{{DB_MIGRATION_NAMING}}

## 🔐 Security Best Practices

### Input Validation

```{{PRIMARY_LANGUAGE}}
{{INPUT_VALIDATION_EXAMPLE}}
```

### Secrets Management

- ❌ **MAI** hardcodare secrets nel codice
- ✅ Usare variabili d'ambiente
- ✅ Utilizzare {{SECRETS_MANAGER}}

### Sanitizzazione

```{{PRIMARY_LANGUAGE}}
{{SANITIZATION_EXAMPLE}}
```

## 🎨 UI/Frontend Specific (se applicabile)

### Component Structure

```{{PRIMARY_LANGUAGE}}
{{COMPONENT_STRUCTURE_EXAMPLE}}
```

### CSS/Styling Conventions

{{STYLING_CONVENTIONS}}

### Accessibilità

- {{A11Y_RULE_1}}
- {{A11Y_RULE_2}}
- {{A11Y_RULE_3}}

## 📦 Git Conventions

### Commit Messages

**Format**: {{COMMIT_MESSAGE_FORMAT}}

**Esempi**:
```
✅ CORRETTO:
{{GOOD_COMMIT_EXAMPLES}}

❌ SBAGLIATO:
{{BAD_COMMIT_EXAMPLES}}
```

### Branch Naming

**Format**: {{BRANCH_NAMING_FORMAT}}

**Esempi**:
```
feature/user-authentication
bugfix/login-error
hotfix/security-patch
```

### Pull Request

**Template**: Vedere [CONTRIBUTING.md](../global/contributing.md)

## 🛠️ Tooling

### Linter

**Tool**: {{LINTER}}

**Configurazione**: `{{LINTER_CONFIG_FILE}}`

**Esecuzione**:
```bash
{{LINTER_COMMAND}}
```

### Formatter

**Tool**: {{FORMATTER}}

**Configurazione**: `{{FORMATTER_CONFIG_FILE}}`

**Esecuzione**:
```bash
{{FORMATTER_COMMAND}}
```

### Pre-commit Hooks

```bash
{{PRE_COMMIT_SETUP}}
```

## 📚 Esempi Completi

### Esempio 1: {{EXAMPLE_1_TITLE}}

```{{PRIMARY_LANGUAGE}}
{{COMPLETE_EXAMPLE_1}}
```

### Esempio 2: {{EXAMPLE_2_TITLE}}

```{{PRIMARY_LANGUAGE}}
{{COMPLETE_EXAMPLE_2}}
```

## ❌ Anti-Patterns da Evitare

### Anti-Pattern 1: {{ANTI_PATTERN_1}}

```{{PRIMARY_LANGUAGE}}
// ❌ SBAGLIATO
{{ANTI_PATTERN_1_BAD}}

// ✅ CORRETTO
{{ANTI_PATTERN_1_GOOD}}
```

### Anti-Pattern 2: {{ANTI_PATTERN_2}}

```{{PRIMARY_LANGUAGE}}
// ❌ SBAGLIATO
{{ANTI_PATTERN_2_BAD}}

// ✅ CORRETTO
{{ANTI_PATTERN_2_GOOD}}
```

## 📖 Risorse Aggiuntive

- {{STYLE_GUIDE_REF_1}}
- {{STYLE_GUIDE_REF_2}}
- {{STYLE_GUIDE_REF_3}}

## ✅ Checklist Code Review

- [ ] Naming conventions rispettate
- [ ] Codice formattato correttamente
- [ ] Commenti dove necessario
- [ ] Error handling appropriato
- [ ] Test presenti e passano
- [ ] Nessun secret hardcodato
- [ ] Performance considerata
- [ ] Accessibilità considerata (se frontend)

---
*Generato automaticamente da Toduba System v{{TODUBA_VERSION}}*
