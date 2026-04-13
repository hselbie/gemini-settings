# Amelia TDD - Usage Examples

Real TDD sessions showing RED → GREEN → REFACTOR cycles.

---

## Example 1: Email Validation

### User Request
```
/amelia-tdd

Add email validation to user registration
```

### Amelia's TDD Cycle

```
TDD cycle: Email validation for registration

Starting with simplest case (valid email).

RED: auth.test.ts:1-10
Test: register() should accept valid email format

it('should accept valid email format', async () => {
  const user = { email: 'test@example.com', password: 'secret' };
  const result = await register(user);
  expect(result.success).toBe(true);
});

Running tests...
FAILING: 1 test red ❌
- should accept valid email format

GREEN phase next.
```

```
GREEN: auth.ts:1-5
Implemented: register() returns success

const register = async (user) => {
  return { success: true };
};

Running tests...
PASSING: 1 test green ✅

REFACTOR: None needed (minimal code).

RED phase for invalid email.
```

```
RED: auth.test.ts:12-22
Test: register() should reject invalid email format

it('should reject invalid email format', async () => {
  const user = { email: 'invalid-email', password: 'secret' };
  await expect(register(user)).rejects.toThrow('Invalid email format');
});

Running tests...
FAILING: 2 tests - 1 green, 1 red
- should accept valid email format ✅
- should reject invalid email format ❌

GREEN phase next.
```

```
GREEN: auth.ts:1-10
Implemented: Email validation with regex

const register = async (user) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(user.email)) {
    throw new Error('Invalid email format');
  }
  return { success: true };
};

Running tests...
PASSING: 2 tests green ✅

REFACTOR: Extract validateEmail helper.
```

```
REFACTOR: auth.ts:1-15
Extracted: validateEmail() helper function

const validateEmail = (email) => {
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!emailRegex.test(email)) {
    throw new Error('Invalid email format');
  }
};

const register = async (user) => {
  validateEmail(user.email);
  return { success: true };
};

Running tests...
PASSING: 2 tests green ✅

TDD cycle complete.

SUMMARY:
- auth.ts:1-15 - register() with email validation
- auth.test.ts:1-22 - 2 tests

Tests:
✅ should accept valid email format
✅ should reject invalid email format

Ready for next behavior.
```

---

## Example 2: Password Hashing

### User Request
```
/amelia-tdd

Add password hashing to registration
```

### Amelia's TDD Cycle

```
TDD cycle: Password hashing

RED: auth.test.ts:24-36
Test: register() should hash password before storing

it('should hash password before storing', async () => {
  const user = { email: 'test@example.com', password: 'secret123' };
  const result = await register(user);

  expect(result.user.password).not.toBe('secret123');
  expect(await bcrypt.compare('secret123', result.user.password)).toBe(true);
});

Running tests...
FAILING: 3 tests - 2 green, 1 red
- should accept valid email format ✅
- should reject invalid email format ✅
- should hash password before storing ❌

GREEN phase next.
```

```
GREEN: auth.ts:10-15
Implemented: Password hashing with bcrypt

const register = async (user) => {
  validateEmail(user.email);

  const hashedPassword = await bcrypt.hash(user.password, 10);
  const userWithHashedPassword = { ...user, password: hashedPassword };

  return { success: true, user: userWithHashedPassword };
};

Running tests...
PASSING: 3 tests green ✅

REFACTOR: Extract hashPassword helper.
```

```
REFACTOR: auth.ts:1-20
Extracted: hashPassword() helper

const hashPassword = async (password) => {
  return bcrypt.hash(password, 10);
};

const register = async (user) => {
  validateEmail(user.email);
  const hashedPassword = await hashPassword(user.password);
  return { success: true, user: { ...user, password: hashedPassword } };
};

Running tests...
PASSING: 3 tests green ✅

TDD cycle complete.

SUMMARY:
- auth.ts:1-20 - register() with email validation + password hashing
- auth.test.ts:1-36 - 3 tests

Tests:
✅ should accept valid email format
✅ should reject invalid email format
✅ should hash password before storing

Coverage: 100% of register function
```

---

## Example 3: Calculator Function (Classic TDD)

### User Request
```
/amelia-tdd

Build an add function using TDD
```

### Amelia's TDD Cycle

```
TDD cycle: add() function

Starting with simplest possible test.

RED: calc.test.ts:1-5
Test: add() should return 5 when adding 2 and 3

it('should return 5 when adding 2 and 3', () => {
  expect(add(2, 3)).toBe(5);
});

Running tests...
FAILING: 1 test red ❌
- should return 5 when adding 2 and 3

GREEN phase next.
```

```
GREEN: calc.ts:1-3
Implemented: add() returns 5 (hardcoded)

const add = () => 5;

Running tests...
PASSING: 1 test green ✅

REFACTOR: None needed.

RED phase for next case.
```

```
RED: calc.test.ts:7-11
Test: add() should return 12 when adding 5 and 7

it('should return 12 when adding 5 and 7', () => {
  expect(add(5, 7)).toBe(12);
});

Running tests...
FAILING: 2 tests - 1 green, 1 red
- should return 5 when adding 2 and 3 ✅
- should return 12 when adding 5 and 7 ❌ (returns 5)

GREEN phase next.
```

```
GREEN: calc.ts:1-3
Implemented: add() with actual addition logic

const add = (a, b) => a + b;

Running tests...
PASSING: 2 tests green ✅

REFACTOR: Code is simple, no refactoring needed.

RED phase for negative numbers.
```

```
RED: calc.test.ts:13-17
Test: add() should handle negative numbers

it('should return -3 when adding -5 and 2', () => {
  expect(add(-5, 2)).toBe(-3);
});

Running tests...
PASSING: 3 tests green ✅ (already works!)

TDD cycle complete.

SUMMARY:
- calc.ts:1-3 - add(a, b) function
- calc.test.ts:1-17 - 3 tests

Tests:
✅ should return 5 when adding 2 and 3
✅ should return 12 when adding 5 and 7
✅ should return -3 when adding -5 and 2

Coverage: 100%
```

---

## Example 4: API Endpoint with Integration Test

### User Request
```
/amelia-tdd

Add POST /users endpoint using TDD
```

### Amelia's TDD Cycle

```
TDD cycle: POST /users endpoint

RED: users.test.ts:1-15
Test: POST /users should create user and return 201

it('should create user and return 201', async () => {
  const newUser = { name: 'John Doe', email: 'john@example.com' };

  const response = await request(app)
    .post('/users')
    .send(newUser);

  expect(response.status).toBe(201);
  expect(response.body.user.name).toBe('John Doe');
  expect(response.body.user.id).toBeDefined();
});

Running tests...
FAILING: 1 test red ❌
- should create user and return 201

GREEN phase next.
```

```
GREEN: routes/users.ts:1-12
Implemented: POST /users endpoint

router.post('/users', async (req, res) => {
  const user = {
    id: 1,
    name: req.body.name,
    email: req.body.email
  };

  res.status(201).json({ user });
});

Running tests...
PASSING: 1 test green ✅

REFACTOR: Extract user creation logic.
```

```
REFACTOR: routes/users.ts + services/user-service.ts
Extracted: createUser() service function

// services/user-service.ts
const createUser = async (userData) => {
  return {
    id: generateId(),
    ...userData
  };
};

// routes/users.ts
router.post('/users', async (req, res) => {
  const user = await createUser(req.body);
  res.status(201).json({ user });
});

Running tests...
PASSING: 1 test green ✅

RED phase for validation.
```

```
RED: users.test.ts:17-27
Test: POST /users should return 400 when name is missing

it('should return 400 when name is missing', async () => {
  const invalidUser = { email: 'john@example.com' };

  const response = await request(app)
    .post('/users')
    .send(invalidUser);

  expect(response.status).toBe(400);
  expect(response.body.error).toBe('Name is required');
});

Running tests...
FAILING: 2 tests - 1 green, 1 red
- should create user and return 201 ✅
- should return 400 when name is missing ❌

GREEN phase next.
```

```
GREEN: routes/users.ts:2-7
Implemented: Name validation

router.post('/users', async (req, res) => {
  if (!req.body.name) {
    return res.status(400).json({ error: 'Name is required' });
  }

  const user = await createUser(req.body);
  res.status(201).json({ user });
});

Running tests...
PASSING: 2 tests green ✅

REFACTOR: Extract validation middleware.
```

```
REFACTOR: middleware/validate-user.ts + routes/users.ts
Extracted: validateUserInput middleware

// middleware/validate-user.ts
const validateUserInput = (req, res, next) => {
  if (!req.body.name) {
    return res.status(400).json({ error: 'Name is required' });
  }
  next();
};

// routes/users.ts
router.post('/users', validateUserInput, async (req, res) => {
  const user = await createUser(req.body);
  res.status(201).json({ user });
});

Running tests...
PASSING: 2 tests green ✅

TDD cycle complete.

SUMMARY:
- routes/users.ts:1-8 - POST /users endpoint
- services/user-service.ts:1-10 - createUser logic
- middleware/validate-user.ts:1-8 - validation
- users.test.ts:1-27 - 2 integration tests

Tests:
✅ should create user and return 201
✅ should return 400 when name is missing

Coverage: POST /users endpoint fully tested
```

---

## Example 5: Refactoring Existing Code with TDD

### User Request
```
/amelia-tdd

This calculateTotal function has no tests and needs refactoring
```

### Amelia's Approach

```
Step 1: Add tests for current behavior (characterization tests)

RED: calc.test.ts:1-10
Test: calculateTotal() should sum item prices

it('should sum item prices', () => {
  const items = [
    { price: 10 },
    { price: 20 },
    { price: 30 }
  ];
  expect(calculateTotal(items)).toBe(60);
});

Running tests...
PASSING: 1 test green ✅ (current behavior captured)
```

```
RED: calc.test.ts:12-22
Test: calculateTotal() should handle empty array

it('should return 0 for empty array', () => {
  expect(calculateTotal([])).toBe(0);
});

Running tests...
PASSING: 2 tests green ✅ (current behavior works)
```

```
RED: calc.test.ts:24-34
Test: calculateTotal() should apply discounts

it('should apply discounts', () => {
  const items = [
    { price: 100, discount: 0.1 }
  ];
  expect(calculateTotal(items)).toBe(90);
});

Running tests...
FAILING: 3 tests - 2 green, 1 red
- should sum item prices ✅
- should return 0 for empty array ✅
- should apply discounts ❌

GREEN phase for discount support.
```

```
GREEN: calc.ts:5-8
Implemented: Discount support

const calculateTotal = (items) => {
  return items.reduce((sum, item) => {
    const discountedPrice = item.price * (1 - (item.discount || 0));
    return sum + discountedPrice;
  }, 0);
};

Running tests...
PASSING: 3 tests green ✅

REFACTOR: Extract price calculation logic.
```

```
REFACTOR: calc.ts:1-15
Extracted: calculateItemPrice() helper

const calculateItemPrice = (item) => {
  return item.price * (1 - (item.discount || 0));
};

const calculateTotal = (items) => {
  return items.reduce((sum, item) => {
    return sum + calculateItemPrice(item);
  }, 0);
};

Running tests...
PASSING: 3 tests green ✅

Refactoring complete with confidence (tests stayed green).

SUMMARY:
- calc.ts:1-15 - Refactored calculateTotal with discount support
- calc.test.ts:1-34 - 3 tests protecting against regressions

Tests:
✅ should sum item prices
✅ should return 0 for empty array
✅ should apply discounts

Safe to refactor further if needed (tests provide safety net).
```

---

## Key Patterns

### Pattern 1: Start with Hardcoded Values (Fake It)

```
RED: Test expects specific value
GREEN: Return that hardcoded value
RED: Test with different input
GREEN: Implement real logic
```

**Example:**
- Test: `expect(add(2, 3)).toBe(5)`
- GREEN: `const add = () => 5` (hardcoded!)
- Test: `expect(add(5, 7)).toBe(12)`
- GREEN: `const add = (a, b) => a + b` (real logic)

### Pattern 2: Triangulation

**Write 2+ tests to force general solution**

```
RED: Test case 1
GREEN: Hardcoded for case 1
RED: Test case 2 (breaks hardcoded solution)
GREEN: General solution that works for both
```

### Pattern 3: One Test at a Time

**Never write multiple failing tests**

```
✅ RED → GREEN → REFACTOR (Test 1)
✅ RED → GREEN → REFACTOR (Test 2)
✅ RED → GREEN → REFACTOR (Test 3)

❌ RED (Test 1) → RED (Test 2) → RED (Test 3) → GREEN all
```

---

## Tips for Working with Amelia

### 1. Embrace RED Phase
Don't skip seeing tests fail. It proves the test works.

### 2. Minimum Code in GREEN
Resist urge to write perfect code immediately. Just make test pass.

### 3. Trust REFACTOR Phase
Tests give you safety net to improve code without fear.

### 4. One Behavior Per Cycle
Don't try to implement entire feature in one cycle.

### 5. Run Tests Constantly
After every small change in REFACTOR phase.

---

## Common Mistakes

### Mistake 1: Implementation Before Test

❌ **Wrong:**
```
User: "Add login function"
[Writes login function first, then tests]
```

✅ **Right:**
```
User: "Add login function"
RED: Write failing test for login
GREEN: Implement login to pass test
```

### Mistake 2: Perfect Code in GREEN Phase

❌ **Wrong:**
```
GREEN: Write beautiful, abstracted, DRY code immediately
```

✅ **Right:**
```
GREEN: Minimum code to pass (ugly is OK!)
REFACTOR: Now make it beautiful
```

### Mistake 3: Skipping REFACTOR

❌ **Wrong:**
```
RED → GREEN → RED → GREEN → (duplicate code everywhere)
```

✅ **Right:**
```
RED → GREEN → REFACTOR → (clean code)
```

---

**END OF EXAMPLES**
