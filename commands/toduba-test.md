---
allowed-tools:
  - Bash
  - Read
  - Grep
  - Glob
  - Task
argument-hint: "[--watch] [--coverage] [--only <pattern>] [--fail-fast]"
description: "Esegue test suite completa con report coverage e watch mode"
model: haiku
---

# Toduba Test - Esecuzione Test Suite 🧪

## Obiettivo
Eseguire test suite completa del progetto con supporto per watch mode, coverage reporting e filtering.

## Argomenti
- `--watch`: Modalità watch per sviluppo continuo
- `--coverage`: Genera report coverage dettagliato
- `--only <pattern>`: Esegue solo test che matchano pattern
- `--fail-fast`: Stop alla prima failure
- `--parallel`: Esegue test in parallelo
- `--verbose`: Output dettagliato

Argomenti ricevuti: $ARGUMENTS

## Progress Tracking
```
🧪 Test Execution Progress
[████████░░░░░░░░] 53% - Running integration tests (27/51)
⏱️ ETA: 2 minutes
✅ Unit: 245/245 | 🔄 Integration: 27/51 | ⏳ E2E: 0/12
```

## Processo di Test

### Fase 1: Auto-Detect Test Framework

```bash
detect_test_framework() {
  echo "🔍 Detecting test framework..."

  if [ -f "package.json" ]; then
    # Node.js project
    if grep -q '"jest"' package.json; then
      TEST_RUNNER="jest"
      TEST_CMD="npm test"
    elif grep -q '"vitest"' package.json; then
      TEST_RUNNER="vitest"
      TEST_CMD="npm test"
    elif grep -q '"mocha"' package.json; then
      TEST_RUNNER="mocha"
      TEST_CMD="npm test"
    elif grep -q '"cypress"' package.json; then
      HAS_E2E="true"
      E2E_CMD="npm run cypress:run"
    elif grep -q '"playwright"' package.json; then
      HAS_E2E="true"
      E2E_CMD="npm run playwright test"
    fi
  elif [ -f "pubspec.yaml" ]; then
    # Flutter project
    TEST_RUNNER="flutter"
    TEST_CMD="flutter test"
  elif [ -f "requirements.txt" ] || [ -f "setup.py" ]; then
    # Python project
    if grep -q "pytest" requirements.txt 2>/dev/null; then
      TEST_RUNNER="pytest"
      TEST_CMD="pytest"
    else
      TEST_RUNNER="unittest"
      TEST_CMD="python -m unittest"
    fi
  elif [ -f "pom.xml" ]; then
    # Java Maven
    TEST_RUNNER="maven"
    TEST_CMD="mvn test"
  elif [ -f "build.gradle" ]; then
    # Java Gradle
    TEST_RUNNER="gradle"
    TEST_CMD="./gradlew test"
  elif [ -f "Cargo.toml" ]; then
    # Rust
    TEST_RUNNER="cargo"
    TEST_CMD="cargo test"
  elif [ -f "go.mod" ]; then
    # Go
    TEST_RUNNER="go"
    TEST_CMD="go test ./..."
  fi

  echo "✅ Detected: $TEST_RUNNER"
}
```

### Fase 2: Parse Arguments e Setup

```bash
# Parse arguments
WATCH_MODE=false
COVERAGE=false
PATTERN=""
FAIL_FAST=false
PARALLEL=false
VERBOSE=false

for arg in $ARGUMENTS; do
  case $arg in
    --watch) WATCH_MODE=true ;;
    --coverage) COVERAGE=true ;;
    --only) PATTERN=$2; shift ;;
    --fail-fast) FAIL_FAST=true ;;
    --parallel) PARALLEL=true ;;
    --verbose) VERBOSE=true ;;
  esac
  shift
done
```

### Fase 3: Esecuzione Test con Progress

```bash
run_tests_with_progress() {
  local total_tests=$(find . -name "*.test.*" -o -name "*.spec.*" | wc -l)
  local current=0

  echo "🧪 Starting Test Suite Execution"
  echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

  # Unit Tests
  if [ -d "src/__tests__" ] || [ -d "test/unit" ]; then
    echo "📦 Running Unit Tests..."

    if [ "$COVERAGE" = true ]; then
      $TEST_CMD -- --coverage
    else
      $TEST_CMD
    fi

    UNIT_RESULT=$?
    ((current+=1))
    show_progress $current $total_tests "Unit tests completed"
  fi

  # Integration Tests
  if [ -d "test/integration" ] || [ -d "tests/integration" ]; then
    echo "🔗 Running Integration Tests..."
    $TEST_CMD integration
    INTEGRATION_RESULT=$?
    ((current+=1))
    show_progress $current $total_tests "Integration tests completed"
  fi

  # E2E Tests
  if [ "$HAS_E2E" = true ]; then
    echo "🌐 Running E2E Tests..."
    $E2E_CMD
    E2E_RESULT=$?
    ((current+=1))
    show_progress $current $total_tests "E2E tests completed"
  fi
}

show_progress() {
  local current=$1
  local total=$2
  local message=$3
  local percent=$((current * 100 / total))
  local filled=$((percent / 5))
  local empty=$((20 - filled))

  printf "\r["
  printf "%${filled}s" | tr ' ' '█'
  printf "%${empty}s" | tr ' ' '░'
  printf "] %d%% - %s\n" $percent "$message"
}
```

### Fase 4: Watch Mode Implementation

```javascript
// Se --watch è attivo
if (WATCH_MODE) {
  console.log('👁️ Watch mode activated - Tests will re-run on file changes');

  const chokidar = require('chokidar');

  const watcher = chokidar.watch(['src/**/*.js', 'test/**/*.js'], {
    ignored: /node_modules/,
    persistent: true
  });

  watcher.on('change', async (path) => {
    console.clear();
    console.log(`📝 File changed: ${path}`);
    console.log('🔄 Re-running tests...\n');

    // Re-run only affected tests
    const affectedTests = findAffectedTests(path);
    await runTests(affectedTests);

    // Update progress
    updateProgress();
  });
}
```

### Fase 5: Coverage Report Generation

```bash
generate_coverage_report() {
  if [ "$COVERAGE" = true ]; then
    echo ""
    echo "📊 Coverage Report"
    echo "━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━"

    # Run with coverage
    if [ "$TEST_RUNNER" = "jest" ]; then
      npm test -- --coverage --coverageReporters=text
    elif [ "$TEST_RUNNER" = "pytest" ]; then
      pytest --cov=. --cov-report=term
    elif [ "$TEST_RUNNER" = "go" ]; then
      go test ./... -cover
    fi

    # Parse coverage
    COVERAGE_PERCENT=$(parse_coverage_output)

    # Visual coverage bar
    show_coverage_bar $COVERAGE_PERCENT

    # Check threshold
    if [ $COVERAGE_PERCENT -lt 80 ]; then
      echo "⚠️  Coverage below 80% threshold!"
      return 1
    else
      echo "✅ Coverage meets threshold"
    fi
  fi
}

show_coverage_bar() {
  local percent=$1
  local filled=$((percent / 5))
  local empty=$((20 - filled))

  echo ""
  echo "Coverage: ["
  printf "%${filled}s" | tr ' ' '🟩'
  printf "%${empty}s" | tr ' ' '⬜'
  printf "] %d%%\n" $percent

  if [ $percent -ge 90 ]; then
    echo "🏆 Excellent coverage!"
  elif [ $percent -ge 80 ]; then
    echo "✅ Good coverage"
  elif [ $percent -ge 70 ]; then
    echo "⚠️  Moderate coverage"
  else
    echo "❌ Poor coverage - needs improvement"
  fi
}
```

### Fase 6: Test Results Summary

```markdown
## 📋 Test Results Summary

**Date**: [TIMESTAMP]
**Duration**: 2m 34s
**Mode**: [watch/single]

### Test Suites
| Type | Passed | Failed | Skipped | Time |
|------|--------|--------|---------|------|
| Unit | 245 | 0 | 2 | 12s |
| Integration | 48 | 2 | 0 | 45s |
| E2E | 12 | 0 | 0 | 1m 37s |
| **Total** | **305** | **2** | **2** | **2m 34s** |

### Failed Tests ❌
1. `integration/api/user.test.js`
   - Test: "should handle concurrent updates"
   - Error: Timeout after 5000ms
   - Line: 145

2. `integration/database/transaction.test.js`
   - Test: "rollback on error"
   - Error: Expected 0, received 1
   - Line: 89

### Coverage Report 📊
```
File            | % Stmts | % Branch | % Funcs | % Lines |
----------------|---------|----------|---------|---------|
All files       |   87.3  |    82.1  |   90.5  |   87.2  |
 src/           |   89.2  |    85.3  |   92.1  |   89.1  |
  api/          |   91.5  |    88.2  |   94.3  |   91.4  |
  components/   |   86.7  |    81.9  |   89.8  |   86.6  |
  services/     |   88.4  |    83.7  |   91.2  |   88.3  |
 utils/         |   92.8  |    90.1  |   95.6  |   92.7  |
```

### Performance Metrics ⚡
- Slowest Test: `e2e/checkout-flow.test.js` (8.2s)
- Fastest Test: `unit/utils/format.test.js` (0.003s)
- Average Time: 0.42s per test
- Parallel Execution: Saved 45s (if enabled)

### Recommendations 💡
1. Fix failing integration tests before deployment
2. Improve coverage in `components/` directory
3. Consider splitting slow E2E test
4. Add missing tests for new payment module
```

## Integration con CI/CD

```yaml
# .github/workflows/test.yml
name: Toduba Test Suite
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run Toduba Tests
        run: |
          /toduba-test --coverage --fail-fast
      - name: Upload Coverage
        uses: codecov/codecov-action@v3
        if: success()
```

## Output Examples

### Success Output
```
🧪 Toduba Test Suite
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[████████████████████] 100% - All tests completed

✅ All 309 tests passed!
📊 Coverage: 87.3%
⏱️ Duration: 2m 34s

Run with --watch for continuous testing
```

### Failure Output
```
🧪 Toduba Test Suite
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[████████████░░░░░░░] 65% - Failed at integration tests

❌ 2 tests failed
📊 Coverage: 73.8% (below threshold)
⏱️ Duration: 1m 12s

See details above. Fix and run: /toduba-test --only failed
```

## Advanced Features

### Parallel Execution
```bash
if [ "$PARALLEL" = true ]; then
  echo "⚡ Running tests in parallel..."
  npm test -- --maxWorkers=4
fi
```

### Test Filtering
```bash
if [ -n "$PATTERN" ]; then
  echo "🔍 Running only tests matching: $PATTERN"
  npm test -- --testNamePattern="$PATTERN"
fi
```

### Fail Fast
```bash
if [ "$FAIL_FAST" = true ]; then
  echo "⚠️ Fail-fast mode: Will stop at first failure"
  npm test -- --bail
fi
```