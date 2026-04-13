# Amelia TDD - Workflow Reference

**The Cycle:** RED → GREEN → REFACTOR (repeat)

---

## Complete TDD Cycle

### Phase RED: Write Failing Test

**Duration:** 2-5 minutes

**Steps:**
1. Identify ONE specific behavior to implement
2. Write test that defines that behavior
3. Run test - it MUST fail
4. Confirm failure reason is correct

**Output:**
```
RED: [file]:[lines]
Test: [description of behavior]

Running tests...
FAILING: X tests - [list] ❌
```

**Example:**
```
RED: auth.test.ts:12-22
Test: register() should reject passwords shorter than 8 characters

Running tests...
FAILING: 1 test red ❌
- register should reject short passwords
```

---

### Phase GREEN: Make Test Pass

**Duration:** 3-10 minutes

**Steps:**
1. Write MINIMUM code to make test pass
2. Hardcoded values acceptable
3. Duplication acceptable
4. Run test - it MUST pass

**Output:**
```
GREEN: [file]:[lines]
Implemented: [description]

Running tests...
PASSING: X tests green ✅
```

**Example:**
```
GREEN: auth.ts:15-20
Implemented: password length validation

if (password.length < 8) {
  throw new Error('Password must be at least 8 characters');
}

Running tests...
PASSING: 1 test green ✅
```

---

### Phase REFACTOR: Improve Code

**Duration:** 2-5 minutes

**Steps:**
1. Look for duplication (DRY principle)
2. Improve variable/function names
3. Extract functions if needed
4. Run tests after EACH change
5. Stop if tests turn red

**Output:**
```
REFACTOR: [file]:[lines]
[Description of improvement]

Running tests...
PASSING: X tests green ✅ (still passing)
```

**Example:**
```
REFACTOR: auth.ts:15-25
Extracted: validatePasswordStrength() helper

const validatePasswordStrength = (password) => {
  if (password.length < 8) {
    throw new Error('Password must be at least 8 characters');
  }
};

Running tests...
PASSING: 1 test green ✅
```

---

## Multi-Behavior Implementation

When implementing feature with multiple behaviors:

```
Behavior 1:
  RED → GREEN → REFACTOR

Behavior 2:
  RED → GREEN → REFACTOR

Behavior 3:
  RED → GREEN → REFACTOR

...

SUMMARY: All tests passing
```

---

## Decision Tree: Test Ordering

### Start Simple
```
1. Happy path (valid inputs)
2. Basic validation (required fields)
3. Edge cases (empty, null, boundary values)
4. Error cases (invalid formats, business rules)
5. Security cases (injection, XSS, etc.)
```

### Example: User Registration

```
Order of tests:
1. ✅ Valid registration succeeds
2. ✅ Email required
3. ✅ Password required
4. ✅ Invalid email format rejected
5. ✅ Short password rejected
6. ✅ Duplicate email rejected
7. ✅ Password hashed before storage
```

---

## Test Structure (AAA Pattern)

Every test follows this structure:

```javascript
it('should [behavior] when [condition]', () => {
  // ARRANGE: Set up test data and mocks
  const user = {
    email: 'test@example.com',
    password: 'secret123'
  };

  // ACT: Execute the behavior
  const result = register(user);

  // ASSERT: Verify the outcome
  expect(result.success).toBe(true);
  expect(result.user.email).toBe('test@example.com');
});
```

---

## Minimum Code Examples

### RED Phase Test
```javascript
it('should return sum of two numbers', () => {
  expect(add(2, 3)).toBe(5);
});

// Status: FAILING ❌ (add function doesn't exist)
```

### GREEN Phase (Hardcoded - Valid!)
```javascript
const add = () => 5;

// Status: PASSING ✅ (but obviously wrong for other inputs)
```

### Next RED Phase
```javascript
it('should return sum of 5 and 7', () => {
  expect(add(5, 7)).toBe(12);
});

// Status: FAILING ❌ (returns 5, not 12)
```

### GREEN Phase (Real Implementation)
```javascript
const add = (a, b) => a + b;

// Status: PASSING ✅ (both tests pass)
```

---

## REFACTOR Phase Checklist

Run through these checks:

### 1. DRY (Don't Repeat Yourself)
- [ ] Any duplicated code?
- [ ] Same logic in multiple places?
- [ ] Extract to helper function?

### 2. Naming
- [ ] Variable names clear?
- [ ] Function names describe behavior?
- [ ] No abbreviations?

### 3. Complexity
- [ ] Functions under 20 lines?
- [ ] Max 2 levels of nesting?
- [ ] Logic easy to understand?

### 4. Magic Numbers
- [ ] Any hardcoded numbers?
- [ ] Extract to named constants?

### 5. Tests Green
- [ ] All tests still passing?
- [ ] Run tests after each refactor step

---

## When to Stop Refactoring

**Stop when:**
- Code is clear and simple
- No obvious duplication
- Tests are green
- Further changes would be premature optimization

**Don't:**
- Over-engineer for future requirements
- Add abstractions for 1 use case
- Optimize before measuring
- Refactor unrelated code

---

## Communication Patterns

### RED Phase
```
RED: [file]:[lines]
Test: [specific behavior description]

Running tests...
FAILING: X tests - Y green, Z red
- [test name] ✅
- [failing test name] ❌
```

### GREEN Phase
```
GREEN: [file]:[lines]
Implemented: [what was added]

[Code snippet if helpful]

Running tests...
PASSING: X tests green ✅
```

### REFACTOR Phase
```
REFACTOR: [file]:[lines]
[Type of refactoring]: [what changed]

Running tests...
PASSING: X tests green ✅ (still passing)
```

### Summary
```
TDD cycle complete.

Files:
- [file]:[lines] - [description]
- [test file]:[lines] - [X tests]

Tests:
✅ [test 1 description]
✅ [test 2 description]
✅ [test 3 description]

Coverage: [X]% of [function/module]

Next behaviors: [if more to add]
```

---

## Handling Edge Cases

### Test Fails in GREEN Phase (Wrong Reason)

```
GREEN: auth.ts:15-20
Implemented: email validation

Running tests...
FAILING: Unexpected error ❌

Issue: Typo in regex pattern

Fixed: auth.ts:17

Running tests...
PASSING: All tests green ✅
```

### Tests Break During REFACTOR

```
REFACTOR: auth.ts:15-25
Extracting: validateEmail helper

Running tests...
FAILING: 2 tests red ❌

Reverting: Refactor introduced bug

Running tests...
PASSING: Back to green ✅

Retrying refactor with different approach...
```

### Scope Too Large

```
User: "Add complete user management system"

Amelia:
Breaking into TDD cycles:

Cycle 1: User registration
  - Valid registration
  - Email validation
  - Password hashing

Cycle 2: User login
  - Valid credentials
  - Invalid credentials
  - Token generation

Cycle 3: User profile
  - Get user
  - Update user
  - Delete user

Starting Cycle 1.

RED: [first test]
```

---

## Test Types by Phase

### Unit Tests (Primary)
**When:** Every TDD cycle
**Focus:** Individual functions, pure logic
**Speed:** <10ms per test

```javascript
describe('hashPassword', () => {
  it('should return bcrypt hash', async () => {
    const hashed = await hashPassword('secret123');
    expect(hashed).not.toBe('secret123');
  });
});
```

### Integration Tests (Secondary)
**When:** After unit tests pass
**Focus:** Components working together
**Speed:** <100ms per test

```javascript
describe('POST /auth/register', () => {
  it('should create user in database', async () => {
    const response = await request(app)
      .post('/auth/register')
      .send({ email: 'test@example.com', password: 'secret123' });

    expect(response.status).toBe(201);
    const userInDb = await User.findOne({ email: 'test@example.com' });
    expect(userInDb).toBeDefined();
  });
});
```

---

## Anti-Patterns

### ❌ Implementation Before Test
```
// WRONG
1. Write register function
2. Write tests for register

// RIGHT
1. RED: Write failing test for register
2. GREEN: Write register function
3. REFACTOR: Improve register
```

### ❌ Multiple Behaviors Per Test
```javascript
// WRONG
it('should handle registration', () => {
  // Tests email validation
  // Tests password hashing
  // Tests database storage
  // Tests token generation
});

// RIGHT
it('should validate email format', () => { ... });
it('should hash password', () => { ... });
it('should store user in database', () => { ... });
it('should return JWT token', () => { ... });
```

### ❌ Skipping REFACTOR
```
// WRONG
RED → GREEN → RED → GREEN → RED → GREEN
(ends with duplicated, messy code)

// RIGHT
RED → GREEN → REFACTOR → RED → GREEN → REFACTOR
```

---

## Quick Reference Card

| Phase | Action | Output | Duration |
|-------|--------|--------|----------|
| **RED** | Write failing test | "FAILING: X red" | 2-5 min |
| **GREEN** | Minimum code to pass | "PASSING: X green" | 3-10 min |
| **REFACTOR** | Improve without breaking | "PASSING: X green (refactored)" | 2-5 min |

**Total per cycle:** 7-20 minutes

**Repeat** until feature complete

---

## Success Indicators

TDD is working well when:
- [ ] Every implementation line has a test
- [ ] Tests were written BEFORE implementation
- [ ] GREEN phase uses minimum code
- [ ] REFACTOR phase improves quality
- [ ] All tests pass consistently
- [ ] New bugs are rare
- [ ] Refactoring is safe and confident

---

**END OF WORKFLOW**
