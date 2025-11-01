# Testing Guide - {{SERVICE_NAME}}

> 🧪 Strategia di testing, tool e best practices
> Ultimo aggiornamento: {{TIMESTAMP}}

## 📋 Overview

**Testing Framework**: {{TEST_FRAMEWORK}}
**Test Runner**: {{TEST_RUNNER}}
**Coverage Tool**: {{COVERAGE_TOOL}}
**Mocking Library**: {{MOCKING_LIBRARY}}

**Current Coverage**:
- **Overall**: {{OVERALL_COVERAGE}}%
- **Statements**: {{STATEMENT_COVERAGE}}%
- **Branches**: {{BRANCH_COVERAGE}}%
- **Functions**: {{FUNCTION_COVERAGE}}%
- **Lines**: {{LINE_COVERAGE}}%

**Target Coverage**: {{TARGET_COVERAGE}}%

## 🎯 Testing Strategy

### Testing Pyramid

```
        /\
       /  \  E2E Tests (10%)
      /────\
     /      \  Integration Tests (30%)
    /────────\
   /          \ Unit Tests (60%)
  /────────────\
```

### Test Categories

1. **Unit Tests** (60%): Test singole unità di codice in isolamento
2. **Integration Tests** (30%): Test interazioni tra moduli
3. **E2E Tests** (10%): Test flussi utente completi

## 🚀 Quick Start

### Running Tests

```bash
# Run all tests
{{RUN_ALL_TESTS_CMD}}

# Run specific test file
{{RUN_SPECIFIC_TEST_CMD}}

# Run with coverage
{{RUN_WITH_COVERAGE_CMD}}

# Run in watch mode
{{RUN_WATCH_MODE_CMD}}

# Run tests matching pattern
{{RUN_PATTERN_CMD}}
```

### Test Structure

```
{{TEST_DIRECTORY_STRUCTURE}}
```

## 🧪 Unit Testing

### Test File Convention

**Location**: `{{UNIT_TEST_LOCATION}}`
**Naming**: `{{UNIT_TEST_NAMING}}`

### Basic Unit Test Template

```{{PRIMARY_LANGUAGE}}
{{UNIT_TEST_TEMPLATE}}
```

### Example: Testing a Function

```{{PRIMARY_LANGUAGE}}
// Source: {{SOURCE_FILE_EXAMPLE}}
{{SOURCE_CODE_EXAMPLE}}

// Test: {{TEST_FILE_EXAMPLE}}
{{UNIT_TEST_EXAMPLE}}
```

### Mocking Dependencies

```{{PRIMARY_LANGUAGE}}
{{MOCKING_EXAMPLE}}
```

### Testing Async Code

```{{PRIMARY_LANGUAGE}}
{{ASYNC_TEST_EXAMPLE}}
```

### Best Practices Unit Tests

- ✅ One assertion per test (o gruppo logico di assertions)
- ✅ Test naming: `should_expectedBehavior_when_condition`
- ✅ AAA Pattern: Arrange, Act, Assert
- ✅ Mock external dependencies
- ✅ Test edge cases and error conditions
- ❌ Don't test implementation details
- ❌ Don't test third-party libraries

## 🔗 Integration Testing

### When to Write Integration Tests

- Testing database interactions
- Testing API endpoints
- Testing multiple modules working together
- Testing external service integrations

### Database Integration Tests

```{{PRIMARY_LANGUAGE}}
{{DB_INTEGRATION_TEST_EXAMPLE}}
```

### API Integration Tests

```{{PRIMARY_LANGUAGE}}
{{API_INTEGRATION_TEST_EXAMPLE}}
```

### Test Database Setup

```bash
# Setup test database
{{TEST_DB_SETUP_CMD}}

# Run migrations
{{TEST_DB_MIGRATE_CMD}}

# Seed test data
{{TEST_DB_SEED_CMD}}

# Cleanup
{{TEST_DB_CLEANUP_CMD}}
```

### Best Practices Integration Tests

- ✅ Use separate test database
- ✅ Reset database state between tests
- ✅ Use transactions and rollback when possible
- ✅ Test realistic scenarios
- ❌ Don't rely on external services (mock them)
- ❌ Don't test too many things at once

## 🌐 End-to-End Testing

### E2E Framework

**Tool**: {{E2E_FRAMEWORK}}
**Browser**: {{E2E_BROWSER}}

### Running E2E Tests

```bash
# Run E2E tests
{{E2E_RUN_CMD}}

# Run in headless mode
{{E2E_HEADLESS_CMD}}

# Run with UI
{{E2E_UI_CMD}}

# Run specific spec
{{E2E_SPECIFIC_CMD}}
```

### E2E Test Example

```{{PRIMARY_LANGUAGE}}
{{E2E_TEST_EXAMPLE}}
```

### E2E Test Structure

```
{{E2E_DIRECTORY_STRUCTURE}}
```

### Best Practices E2E Tests

- ✅ Test critical user flows
- ✅ Use data-testid attributes
- ✅ Keep tests independent
- ✅ Use Page Object pattern
- ❌ Don't test every edge case (use unit tests)
- ❌ Don't make tests too brittle
- ❌ Avoid testing third-party components

## 📊 Test Coverage

### Viewing Coverage Reports

```bash
# Generate coverage report
{{GENERATE_COVERAGE_CMD}}

# Open HTML report
{{OPEN_COVERAGE_REPORT_CMD}}
```

### Coverage Thresholds

```json
{
  "coverage": {
    "global": {
      "statements": {{COVERAGE_THRESHOLD_STATEMENTS}},
      "branches": {{COVERAGE_THRESHOLD_BRANCHES}},
      "functions": {{COVERAGE_THRESHOLD_FUNCTIONS}},
      "lines": {{COVERAGE_THRESHOLD_LINES}}
    }
  }
}
```

### Coverage Exemptions

```{{PRIMARY_LANGUAGE}}
/* istanbul ignore next */
{{COVERAGE_IGNORE_EXAMPLE}}
```

**When to ignore coverage**:
- ✅ Error handlers for unexpected errors
- ✅ Development-only code
- ✅ Third-party code wrappers
- ❌ Business logic
- ❌ Core functionality

## 🎭 Test Doubles

### Mocks

```{{PRIMARY_LANGUAGE}}
{{MOCK_EXAMPLE}}
```

### Stubs

```{{PRIMARY_LANGUAGE}}
{{STUB_EXAMPLE}}
```

### Spies

```{{PRIMARY_LANGUAGE}}
{{SPY_EXAMPLE}}
```

### Fakes

```{{PRIMARY_LANGUAGE}}
{{FAKE_EXAMPLE}}
```

## 🔧 Test Utilities

### Common Test Helpers

```{{PRIMARY_LANGUAGE}}
{{TEST_HELPERS_EXAMPLE}}
```

### Test Fixtures

```{{PRIMARY_LANGUAGE}}
{{TEST_FIXTURES_EXAMPLE}}
```

### Factory Functions

```{{PRIMARY_LANGUAGE}}
{{FACTORY_FUNCTIONS_EXAMPLE}}
```

## ⚡ Performance Testing

### Load Testing

**Tool**: {{LOAD_TEST_TOOL}}

```bash
# Run load test
{{LOAD_TEST_CMD}}
```

### Benchmark Tests

```{{PRIMARY_LANGUAGE}}
{{BENCHMARK_TEST_EXAMPLE}}
```

## 🔐 Security Testing

### Input Validation Tests

```{{PRIMARY_LANGUAGE}}
{{SECURITY_VALIDATION_TEST}}
```

### Authentication Tests

```{{PRIMARY_LANGUAGE}}
{{AUTH_TEST_EXAMPLE}}
```

### SQL Injection Tests

```{{PRIMARY_LANGUAGE}}
{{SQL_INJECTION_TEST}}
```

## 🐛 Debugging Tests

### Running Single Test

```bash
# Debug specific test
{{DEBUG_SINGLE_TEST_CMD}}
```

### Using Debugger

```{{PRIMARY_LANGUAGE}}
{{DEBUGGER_EXAMPLE}}
```

### Verbose Output

```bash
# Run with verbose output
{{VERBOSE_TEST_CMD}}
```

## 🔄 CI/CD Integration

### GitHub Actions / GitLab CI

```yaml
{{CI_TESTING_CONFIG}}
```

### Pre-commit Hooks

```bash
# Run tests before commit
{{PRE_COMMIT_TEST_HOOK}}
```

### Test Automation

- **On commit**: Run unit tests
- **On PR**: Run unit + integration tests
- **On merge to main**: Run full test suite + E2E
- **Nightly**: Run full suite + load tests

## 📝 Test Data Management

### Test Data Factory

```{{PRIMARY_LANGUAGE}}
{{TEST_DATA_FACTORY}}
```

### Seeding Test Data

```bash
{{SEED_TEST_DATA_CMD}}
```

### Cleaning Up Test Data

```{{PRIMARY_LANGUAGE}}
{{CLEANUP_TEST_DATA}}
```

## 🎯 Testing Best Practices

### General Principles

1. **F.I.R.S.T. Principles**:
   - **F**ast: Tests should run quickly
   - **I**ndependent: Tests shouldn't depend on each other
   - **R**epeatable: Same results every time
   - **S**elf-validating: Pass or fail, no manual checking
   - **T**imely: Written alongside or before production code

2. **AAA Pattern**:
   - **Arrange**: Setup test data and preconditions
   - **Act**: Execute the code being tested
   - **Assert**: Verify expected outcomes

3. **Given-When-Then** (BDD style):
   - **Given**: Initial context
   - **When**: Action occurs
   - **Then**: Expected outcome

### Code Organization

```{{PRIMARY_LANGUAGE}}
{{TEST_ORGANIZATION_EXAMPLE}}
```

### Naming Conventions

**Test Files**: `{{TEST_FILE_NAMING_CONVENTION}}`

**Test Functions**: `{{TEST_FUNCTION_NAMING}}`

**Examples**:
```
✅ Good:
- test_user_creation_with_valid_data()
- should_return_error_when_email_invalid()
- it_should_save_user_to_database()

❌ Bad:
- test1()
- user_test()
- testFunction()
```

## 🚫 Common Pitfalls

### ❌ Testing Implementation Details

```{{PRIMARY_LANGUAGE}}
// BAD: Testing internal state
{{BAD_IMPLEMENTATION_TEST}}

// GOOD: Testing behavior
{{GOOD_BEHAVIOR_TEST}}
```

### ❌ Flaky Tests

**Causes**:
- Timing issues (race conditions)
- External dependencies
- Shared state between tests
- Non-deterministic behavior

**Solutions**:
- Use proper async/await
- Mock external services
- Clean up state in beforeEach/afterEach
- Use deterministic test data

### ❌ Slow Tests

**Optimization strategies**:
- Run unit tests in parallel
- Use in-memory database for tests
- Mock external calls
- Optimize test data setup

## 📚 Resources

### Documentation

- [{{TEST_FRAMEWORK}} Docs]({{TEST_FRAMEWORK_DOCS}})
- [{{E2E_FRAMEWORK}} Docs]({{E2E_FRAMEWORK_DOCS}})
- [Testing Best Practices]({{TESTING_BEST_PRACTICES_URL}})

### Useful Tools

- {{TOOL_1}}: {{TOOL_1_PURPOSE}}
- {{TOOL_2}}: {{TOOL_2_PURPOSE}}
- {{TOOL_3}}: {{TOOL_3_PURPOSE}}

## 🔗 Related Documentation

- [Style Guide](./style-guide.md) - Code conventions
- [Contributing](../global/contributing.md) - How to contribute
- [CI/CD](../operations/ci-cd.md) - Continuous integration setup

---
*Generato automaticamente da Toduba System v{{TODUBA_VERSION}}*
