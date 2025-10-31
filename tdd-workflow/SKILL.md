---
name: tdd-workflow
description: Test-Driven Development workflow for implementing features with proper test coverage and systematic development approach
---

# Test-Driven Development Workflow

## Overview

Test-Driven Development workflow for implementing features with confidence. This skill ensures tests are written first and implementation follows a systematic RED → GREEN → REFACTOR cycle.

**Announce at start:** "I'm using the tdd-workflow skill to implement this feature with test-driven development."

**Dependencies:** Works best after using `context-gather` skill to understand requirements

## When to Use

Use this skill when:
- Implementing new features with clear acceptance criteria
- You want to ensure proper test coverage
- Building critical functionality that needs reliability
- Working on code that will be maintained long-term
- Requirements are clear enough to write tests

## TDD Process Overview

```
┌──────────────┐
│   SETUP      │ Gather context, understand requirements
└──────┬───────┘
       │
┌──────▼───────┐
│   RED        │ Write failing test
└──────┬───────┘
       │
┌──────▼───────┐
│   GREEN      │ Make test pass (minimal code)
└──────┬───────┘
       │
┌──────▼───────┐
│  REFACTOR    │ Clean up code
└──────┬───────┘
       │
       │ More work?
       │ Yes → Back to RED
       │ No → Done
       └─────────────────┐
                         │
                    ┌────▼────┐
                    │  DONE   │
                    └─────────┘
```

## Detailed TDD Cycle

### SETUP PHASE

**Step 1: Gather Context**

Use `context-gather` skill if you haven't already. You need:
- Clear requirements
- Acceptance criteria
- Understanding of what to build
- Knowledge of existing patterns

**Step 2: Identify First Unit of Work**

- Read requirements carefully
- Identify the **first** small unit of functionality
- Break down into testable behaviors
- Start with the simplest case

### RED PHASE

**Step 3: Write the Failing Test**

Write a test that:
- ✓ Tests one small behavior (not multiple things)
- ✓ Aligns with acceptance criteria
- ✓ Has a clear, descriptive name
- ✓ Has meaningful assertions

**Example (JavaScript/TypeScript):**
```typescript
describe('User Login', () => {
  it('should return success when credentials are valid', async () => {
    const result = await login('user@example.com', 'password123');

    expect(result.success).toBe(true);
    expect(result.user).toBeDefined();
    expect(result.user.email).toBe('user@example.com');
  });
});
```

**Example (Python):**
```python
def test_user_can_login_with_valid_credentials():
    """Users should be able to log in with email and password"""
    result = login("user@example.com", "password123")

    assert result.success == True
    assert result.user_id is not None
    assert result.user.email == "user@example.com"
```

**Step 4: Run Test to Verify It Fails**

```bash
# Run the test
npm test  # or pytest, or your test command

# Expected output:
FAILED: test_user_can_login_with_valid_credentials
Error: login is not defined
```

- ✗ Test must fail for the right reason
- Failure message should be clear
- This proves the test is actually testing something

### GREEN PHASE

**Step 5: Implement Minimal Code**

Write the **minimum** code to make the test pass:
- Don't add extra features
- Don't over-engineer
- Just make it green

**Example:**
```typescript
async function login(email: string, password: string): Promise<LoginResult> {
  // Minimal implementation to pass test
  if (email && password) {
    return {
      success: true,
      user: { email, id: '123' }
    };
  }
  return { success: false, user: null };
}
```

**Step 6: Run Test to Verify It Passes**

```bash
# Run the test again
npm test

# Expected output:
PASSED: test_user_can_login_with_valid_credentials
All tests: 1 passed, 0 failed
```

- ✓ Test passes
- ✓ No other tests broken
- ✓ Implementation is simple but functional

### REFACTOR PHASE

**Step 7: Clean Up Code**

Improve code quality while keeping tests green:
- Extract functions for clarity
- Remove duplication
- Improve naming
- Add comments if needed
- Follow project conventions

**Example Refactoring:**
```typescript
async function login(email: string, password: string): Promise<LoginResult> {
  if (!isValidEmail(email) || !isValidPassword(password)) {
    return createFailureResult();
  }

  const user = await findUserByEmail(email);
  if (!user || !await verifyPassword(password, user.passwordHash)) {
    return createFailureResult();
  }

  return createSuccessResult(user);
}
```

**Step 8: Run Tests After Refactoring**

```bash
# Run tests to ensure refactoring didn't break anything
npm test

# Expected output:
PASSED: All tests passing
```

- ✓ All tests still pass
- ✓ No regressions introduced
- ✓ Code is cleaner and more maintainable

### REPEAT PHASE

**Step 9: Move to Next Functionality**

1. Identify next small unit of work
2. Return to Step 3 (RED phase)
3. Repeat RED → GREEN → REFACTOR until feature is complete

**Example - Next Test:**
```typescript
it('should return failure when password is incorrect', async () => {
  const result = await login('user@example.com', 'wrong-password');

  expect(result.success).toBe(false);
  expect(result.error).toBe('Invalid credentials');
});
```

## Best Practices

### DO:
- ✓ Write smallest possible test first
- ✓ Make test fail before writing implementation
- ✓ Write minimal code to pass the test
- ✓ Refactor only after tests are green
- ✓ Run tests frequently (after every small change)
- ✓ Write clear, descriptive test names
- ✓ Test one behavior per test
- ✓ Keep tests independent and isolated

### DON'T:
- ✗ Skip writing tests first
- ✗ Write implementation before test
- ✗ Write large tests covering multiple behaviors
- ✗ Add features not required by current test
- ✗ Refactor while tests are failing
- ✗ Write vague test names like "test1"
- ✗ Make tests depend on each other
- ✗ Skip the refactor step

## Testing Frameworks by Language

### JavaScript/TypeScript
- **Jest**: Most popular, great for React
- **Vitest**: Fast, modern alternative to Jest
- **Mocha + Chai**: Flexible testing framework
- **Playwright/Cypress**: E2E testing

### Python
- **pytest**: Most popular, powerful assertions
- **unittest**: Built-in standard library
- **nose2**: Extension of unittest

### Other Languages
- **Java**: JUnit, TestNG
- **C#**: NUnit, xUnit
- **Go**: Built-in testing package
- **Ruby**: RSpec, Minitest

## Example End-to-End Flow

**Scenario**: Implementing user registration

```bash
# 1. SETUP
Understand requirement: Users can register with email and password

# 2. RED - Write failing test
Write: test_user_can_register_with_valid_email()
Run: FAILED (register function not defined)

# 3. GREEN - Minimal implementation
Write: function register(email, password) { return { success: true } }
Run: PASSED

# 4. REFACTOR - Clean up
Extract validation logic
Add proper error handling
Run: PASSED (still green)

# 5. REPEAT - Next test
Write: test_user_cannot_register_with_invalid_email()
... continue cycle
```

## Common Patterns

### Testing API Endpoints
```typescript
describe('POST /api/users', () => {
  it('should create user with valid data', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ email: 'test@example.com', password: 'password123' });

    expect(response.status).toBe(201);
    expect(response.body.user).toBeDefined();
  });
});
```

### Testing React Components
```typescript
describe('LoginForm', () => {
  it('should call onSubmit when form is submitted', () => {
    const onSubmit = jest.fn();
    render(<LoginForm onSubmit={onSubmit} />);

    fireEvent.change(screen.getByLabelText('Email'), {
      target: { value: 'test@example.com' }
    });
    fireEvent.click(screen.getByRole('button', { name: 'Login' }));

    expect(onSubmit).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: expect.any(String)
    });
  });
});
```

### Testing Database Operations
```python
def test_user_is_saved_to_database():
    user = User(email="test@example.com", name="Test User")
    user.save()

    retrieved_user = User.get_by_email("test@example.com")
    assert retrieved_user.name == "Test User"
```

## Success Criteria

A successful TDD cycle includes:
- ✓ Test written before implementation
- ✓ Test failed initially for the right reason
- ✓ Implementation makes test pass
- ✓ Code is refactored and clean
- ✓ All tests passing
- ✓ Feature meets acceptance criteria
- ✓ Code is maintainable and well-structured

## Benefits of TDD

1. **Confidence**: Tests prove your code works
2. **Design**: Writing tests first leads to better design
3. **Documentation**: Tests document expected behavior
4. **Refactoring**: Tests enable safe refactoring
5. **Debugging**: Failing tests pinpoint issues quickly
6. **Regression Prevention**: Tests catch bugs early

## When TDD Might Not Fit

TDD is great, but not always the best approach for:
- Quick prototypes or experiments
- UI design exploration
- Very unclear requirements
- Highly visual/interactive work that needs experimentation
- Integration with poorly documented third-party APIs

In these cases, write tests after implementation or use a hybrid approach.

## Tips for Success

1. **Start Simple**: Begin with the easiest test case
2. **One Step at a Time**: Don't try to implement everything at once
3. **Keep Tests Fast**: Fast tests encourage frequent running
4. **Isolate Tests**: Use mocks/stubs for external dependencies
5. **Good Names**: Test names should describe behavior
6. **Clean Tests**: Tests should be as clean as production code
7. **Delete Redundant Tests**: Remove tests that don't add value

## Example Test Names

Good test names describe behavior:
- ✓ `should return error when email is invalid`
- ✓ `calculates total price including tax`
- ✓ `authenticates user with valid credentials`
- ✓ `throws exception when user not found`

Bad test names are vague:
- ✗ `test1`
- ✗ `testLogin`
- ✗ `itWorks`
- ✗ `checkEmail`

Remember: TDD is a discipline that becomes easier with practice. Start small, stay consistent, and you'll build better software with confidence.
