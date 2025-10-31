---
allowed-tools:
  - Read
  - Grep
  - Glob
  - Bash
  - Task
argument-hint: "[file-or-directory] [--strict]"
description: "Esegue code review approfondita del codice"
model: sonnet
---

# Toduba Code Review - Analisi e Review del Codice 🔍

## Obiettivo
Eseguire una code review completa e dettagliata, fornendo feedback costruttivo su qualità, best practices, sicurezza e manutenibilità.

## Argomenti
- `file-or-directory`: File o directory da revieware (default: current directory)
- `--strict`: Modalità strict con controlli aggiuntivi

Argomenti ricevuti: $ARGUMENTS

## Processo di Code Review

### Fase 1: Identificazione Scope

```bash
# Determina cosa revieware
if [ -z "$ARGUMENTS" ]; then
  # Review ultime modifiche
  FILES=$(git diff --name-only HEAD~1)
else
  # Review file/directory specificato
  FILES=$ARGUMENTS
fi

# Conta file da revieware
FILE_COUNT=$(echo "$FILES" | wc -l)
echo "📋 File da revieware: $FILE_COUNT"
```

### Fase 2: Analisi Multi-Dimensionale

#### 2.1 Code Quality
```typescript
const reviewCodeQuality = (code: string) => {
  const issues = [];

  // Naming conventions
  if (!/^[a-z][a-zA-Z0-9]*$/.test(variableName)) {
    issues.push({
      severity: 'minor',
      type: 'naming',
      message: 'Variable should use camelCase'
    });
  }

  // Function length
  if (functionLines > 50) {
    issues.push({
      severity: 'major',
      type: 'complexity',
      message: 'Function too long, consider splitting'
    });
  }

  // Cyclomatic complexity
  if (complexity > 10) {
    issues.push({
      severity: 'major',
      type: 'complexity',
      message: 'High complexity, simplify logic'
    });
  }

  return issues;
};
```

#### 2.2 Security Review
```typescript
const securityReview = (code: string) => {
  const vulnerabilities = [];

  // SQL Injection
  if (code.includes('query("SELECT * FROM users WHERE id = " + userId)')) {
    vulnerabilities.push({
      severity: 'critical',
      type: 'sql-injection',
      message: 'Use parameterized queries'
    });
  }

  // XSS
  if (code.includes('innerHTML') && !code.includes('sanitize')) {
    vulnerabilities.push({
      severity: 'high',
      type: 'xss',
      message: 'Sanitize HTML before innerHTML'
    });
  }

  // Hardcoded secrets
  if (/api[_-]?key\s*=\s*["'][^"']+["']/i.test(code)) {
    vulnerabilities.push({
      severity: 'critical',
      type: 'secrets',
      message: 'Use environment variables for secrets'
    });
  }

  return vulnerabilities;
};
```

#### 2.3 Performance Review
```typescript
const performanceReview = (code: string) => {
  const issues = [];

  // N+1 queries
  if (code.includes('forEach') && code.includes('await')) {
    issues.push({
      severity: 'major',
      type: 'performance',
      message: 'Potential N+1 query, use batch operations'
    });
  }

  // Memory leaks
  if (code.includes('addEventListener') && !code.includes('removeEventListener')) {
    issues.push({
      severity: 'major',
      type: 'memory',
      message: 'Remove event listeners to prevent memory leaks'
    });
  }

  return issues;
};
```

### Fase 3: Best Practices Check

```typescript
const checkBestPractices = () => {
  const checks = {
    errorHandling: checkErrorHandling(),
    testing: checkTestCoverage(),
    documentation: checkDocumentation(),
    accessibility: checkAccessibility(),
    i18n: checkInternationalization()
  };

  return generateReport(checks);
};
```

### Fase 4: Generazione Report

## Code Review Report Template

```markdown
# 📊 Toduba Code Review Report

**Date**: [TIMESTAMP]
**Reviewer**: Toduba System
**Files Reviewed**: [COUNT]
**Overall Score**: 7.5/10

## 🎯 Summary

### Statistics
- Lines of Code: 450
- Complexity: Medium
- Test Coverage: 78%
- Documentation: Good

### Rating by Category
| Category | Score | Status |
|----------|-------|--------|
| Code Quality | 8/10 | ✅ Good |
| Security | 7/10 | ⚠️ Needs Attention |
| Performance | 8/10 | ✅ Good |
| Maintainability | 7/10 | ⚠️ Moderate |
| Testing | 6/10 | ⚠️ Improve |

## 🔴 Critical Issues (Must Fix)

### 1. SQL Injection Vulnerability
**File**: `src/api/users.js:45`
```javascript
// ❌ Current
const query = "SELECT * FROM users WHERE id = " + userId;

// ✅ Suggested
const query = "SELECT * FROM users WHERE id = ?";
db.query(query, [userId]);
```
**Impact**: High security risk
**Effort**: Low

### 2. Hardcoded API Key
**File**: `src/config.js:12`
```javascript
// ❌ Current
const API_KEY = "sk-1234567890abcdef";

// ✅ Suggested
const API_KEY = process.env.API_KEY;
```

## 🟡 Major Issues (Should Fix)

### 1. Function Complexity
**File**: `src/services/payment.js:120`
- Cyclomatic complexity: 15 (threshold: 10)
- Suggestion: Split into smaller functions
- Example refactoring provided below

### 2. Missing Error Handling
**File**: `src/controllers/user.js:34`
```javascript
// ❌ Current
const user = await getUserById(id);
return res.json(user);

// ✅ Suggested
try {
  const user = await getUserById(id);
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  return res.json(user);
} catch (error) {
  logger.error('Failed to get user:', error);
  return res.status(500).json({ error: 'Internal server error' });
}
```

## 🔵 Minor Issues (Nice to Have)

### 1. Naming Convention
- `getUserData` → `fetchUserData` (more descriptive)
- `tmp` → `temporaryFile` (avoid abbreviations)

### 2. Code Duplication
- Similar logic in 3 places
- Consider extracting to utility function

## ✅ Good Practices Observed

1. **Consistent formatting** throughout the codebase
2. **TypeScript usage** for type safety
3. **Async/await** properly used
4. **Environment variables** for configuration
5. **Modular structure** with clear separation

## 📈 Improvements Since Last Review

- Test coverage increased from 65% to 78%
- Removed 3 deprecated dependencies
- Fixed 2 security vulnerabilities

## 💡 Recommendations

### Immediate Actions
1. Fix SQL injection vulnerability
2. Remove hardcoded secrets
3. Add error handling to async operations

### Short-term Improvements
1. Increase test coverage to 85%
2. Reduce function complexity
3. Add JSDoc comments

### Long-term Suggestions
1. Implement automated security scanning
2. Set up performance monitoring
3. Create coding standards document

## 📝 Detailed Feedback by File

### `src/api/users.js`
- **Lines**: 245
- **Issues**: 3 critical, 2 major, 5 minor
- **Suggestions**:
  - Add input validation middleware
  - Implement rate limiting
  - Use transaction for multi-step operations

### `src/components/UserProfile.tsx`
- **Lines**: 180
- **Issues**: 1 major, 3 minor
- **Suggestions**:
  - Memoize expensive calculations
  - Add loading states
  - Improve accessibility

## 🎓 Learning Opportunities

Based on this review, consider studying:
1. OWASP Top 10 Security Risks
2. Clean Code principles
3. Performance optimization techniques
4. Advanced TypeScript patterns
```

## Integrazione con Orchestrator

Quando chiamato dall'orchestrator:
```typescript
// Può invocare agenti specializzati per review approfondite
if (needsSecurityReview) {
  await Task.invoke('toduba-qa-engineer', {
    action: 'security-scan',
    files: criticalFiles
  });
}

if (needsPerformanceReview) {
  await Task.invoke('toduba-backend-engineer', {
    action: 'performance-analysis',
    files: backendFiles
  });
}
```

## Output Finale

```
✅ Code Review Completata

📊 Risultati:
- Score: 7.5/10
- Critical Issues: 2
- Major Issues: 5
- Minor Issues: 12

🔴 Azioni Richieste:
1. Fix SQL injection (users.js:45)
2. Remove hardcoded API key (config.js:12)

📋 Report completo salvato in:
./code-review-report-2024-10-31.md

💡 Prossimi step:
1. Correggere issue critiche
2. Pianificare fix per issue major
3. Aggiornare documentazione

Tempo impiegato: 45 secondi
```

## Best Practices Code Review
1. **Constructive feedback** sempre
2. **Prioritize issues** per severity
3. **Provide solutions** non solo problemi
4. **Recognize good code** non solo criticare
5. **Educational approach** per team growth
6. **Automated checks** dove possibile
7. **Consistent standards** across reviews
8. **Follow-up** su issue risolte