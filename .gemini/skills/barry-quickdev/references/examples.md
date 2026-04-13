# Barry Quick Dev - Usage Examples

Real-world examples of working with Barry.

---

## Example 1: New Feature - API Endpoint

### User Request
```
/barry-quickdev

Add a user profile endpoint that returns user details and their last 5 posts
```

### Barry's Response
```
Building: GET /profile/:userId endpoint with user details + 5 recent posts

Proceeding.
```

```
Profile endpoint in 2 files:

Files:
- routes/profile.ts (new)
- __tests__/profile.test.ts (new)

Logic:
- Fetch user by ID
- Fetch last 5 posts (ORDER BY created_at DESC LIMIT 5)
- JOIN and return combined response
- 404 if user not found

Proceeding. 12 min.
```

[Implementation]

```
Created: routes/profile.ts:1-34
- GET /profile/:userId endpoint
- Single JOIN query (user + posts)
- Returns user object with posts array

Created: __tests__/profile.test.ts:1-45
- 4 tests: valid user, invalid user (404), posts ordering, no posts case

Self-review:
✅ Security: User ID validated, no SQL injection
✅ Performance: Single JOIN query (not N+1)
✅ Maintainability: Clear structure, 34 lines
✅ Tests: 4 tests, critical paths covered

Done:
- routes/profile.ts:1-34
- __tests__/profile.test.ts:1-45 (4 tests passing)

Tested: valid user, invalid user (404), posts ordering, empty posts

Query optimized: Single JOIN instead of 2 separate queries.

Ship it?
```

---

## Example 2: Bug Fix

### User Request
```
/barry-quickdev

Bug: When users upload files larger than 10MB, the server crashes
```

### Barry's Response
```
Bug: Missing file size validation + error handling

Fix in 1 file: middleware/upload.ts

Issues:
1. No file size limit configured
2. multer error not caught
3. Server crashes on memory overflow

Solution:
- Add 10MB limit to multer config
- Error middleware for multer errors
- Return 413 (Payload Too Large)

Proceeding. 5 min.
```

[Implementation]

```
Modified: middleware/upload.ts:12-28
- Added limits: { fileSize: 10MB }
- Error middleware catches MulterError
- Returns 413 with clear message

Self-review:
✅ Security: File size enforced at middleware level
✅ Performance: Rejects large files early
✅ Maintainability: Clear error messages
✅ Tests: Added 3 test cases

Modified: __tests__/upload.test.ts:45-67
- Test: 5MB file (success)
- Test: 15MB file (413)
- Test: non-file upload (400)

Done:
- middleware/upload.ts:12-28 - File size validation
- __tests__/upload.test.ts:45-67 - 3 new tests

Tested: 5MB file (ok), 15MB file (413), invalid upload (400)

Ship it?
```

---

## Example 3: MVP Feature

### User Request
```
/barry-quickdev

Build a simple blog system with posts and comments. MVP only.
```

### Barry's Response
```
Blog MVP in 5 files:

Files:
- models/Post.ts (new)
- models/Comment.ts (new)
- routes/posts.ts (new)
- routes/comments.ts (new)
- __tests__/blog.test.ts (new)

Features:
- Create/read/update/delete posts
- Add comments to posts
- List all posts (newest first)
- Get post with comments

NOT in MVP:
- User auth (add later)
- Rich text editor (plain text)
- Pagination (add at 100+ posts)
- Comment threading (flat comments)

Proceeding. 25 min.
```

[Implementation]

```
Created: models/Post.ts:1-25
- Post schema: title, content, createdAt
- Mongoose model with indexes

Created: models/Comment.ts:1-20
- Comment schema: text, postId, createdAt
- References Post model

Created: routes/posts.ts:1-95
- POST /posts - Create post
- GET /posts - List all (sorted by date)
- GET /posts/:id - Get single post with comments
- PUT /posts/:id - Update post
- DELETE /posts/:id - Delete post

Created: routes/comments.ts:1-40
- POST /posts/:id/comments - Add comment
- GET /posts/:id/comments - List comments for post

Created: __tests__/blog.test.ts:1-120
- 8 tests covering CRUD + comments

Self-review:
✅ Security: Input validation on all endpoints
✅ Performance: Indexed queries, single JOIN for post+comments
✅ Maintainability: Clear separation (models, routes, tests)
✅ Tests: 8 tests, all critical paths

Done:
- models/Post.ts:1-25
- models/Comment.ts:1-20
- routes/posts.ts:1-95
- routes/comments.ts:1-40
- __tests__/blog.test.ts:1-120 (8 tests passing)

Tested: CRUD posts, add comments, get post with comments, list posts

Database: MongoDB (good for nested comments later)

Next steps:
1. Add user auth (tie posts/comments to users)
2. Add pagination (when > 100 posts)
3. Add rich text support

Ship it?
```

---

## Example 4: Refactoring

### User Request
```
/barry-quickdev

This auth.ts file is 400 lines. Can you break it up?
```

### Barry's Response
```
Refactor: Split auth.ts (400 lines) → 3 focused files

New structure:
1. services/auth-service.ts - Business logic (120 lines)
2. routes/auth-routes.ts - API endpoints (80 lines)
3. middleware/auth-middleware.ts - JWT validation (45 lines)

Keep: __tests__/auth.test.ts (update imports)

Minimal changes: Moving code, not rewriting.

Proceeding. 18 min.
```

[Implementation]

```
Created: services/auth-service.ts:1-120
- register(email, password)
- login(email, password)
- verifyToken(token)
- refreshToken(token)

Created: routes/auth-routes.ts:1-80
- POST /register
- POST /login
- POST /refresh
- GET /me

Created: middleware/auth-middleware.ts:1-45
- authenticateToken middleware
- Extracts and verifies JWT

Deleted: auth.ts (was 400 lines)

Modified: __tests__/auth.test.ts:1-5
- Updated imports to new file structure

Self-review:
✅ Security: No changes to security logic
✅ Performance: No performance impact
✅ Maintainability: Each file single responsibility
✅ Tests: All 12 existing tests still passing

Done:
- services/auth-service.ts:1-120 - Business logic
- routes/auth-routes.ts:1-80 - API routes
- middleware/auth-middleware.ts:1-45 - JWT middleware
- auth.ts - DELETED (400 → 0 lines)
- __tests__/auth.test.ts:1-5 - Updated imports

Tested: All 12 existing tests passing

Structure: 400 lines → 3 files (120 + 80 + 45 = 245 lines)
Removed: Duplicate code, unused functions

Ship it?
```

---

## Example 5: External API Integration

### User Request
```
/barry-quickdev

Add Stripe payment integration for subscription checkout
```

### Barry's Response
```
Stripe subscription checkout in 3 files:

Files:
- services/stripe-service.ts (new) - Stripe API wrapper
- routes/checkout.ts (new) - Checkout endpoints
- __tests__/checkout.test.ts (new) - Tests with mocked Stripe

Flow:
- User clicks "Subscribe"
- Create Stripe Checkout Session
- Redirect to Stripe hosted page
- Webhook handles payment success

Proceeding. 20 min.
```

[Implementation]

```
Created: services/stripe-service.ts:1-67
- createCheckoutSession(priceId, customerId)
- createCustomer(email, name)
- getSubscription(subscriptionId)
- Configured with env vars (STRIPE_SECRET_KEY)

Created: routes/checkout.ts:1-45
- POST /checkout/create-session - Create Stripe session
- Returns sessionId and redirectUrl

Modified: routes/webhooks.ts:78-112
- POST /webhooks/stripe - Handle payment events
- Verifies webhook signature
- Updates user subscription status on success

Created: __tests__/checkout.test.ts:1-89
- Mocked Stripe SDK
- 6 tests: create session, redirect flow, webhook validation, failed payment

Self-review:
✅ Security: Webhook signature verified, secrets in env vars
✅ Performance: Async Stripe calls, webhook processing in background
✅ Maintainability: Clear service abstraction
✅ Tests: 6 tests with mocked Stripe

Done:
- services/stripe-service.ts:1-67 - Stripe wrapper
- routes/checkout.ts:1-45 - Checkout endpoint
- routes/webhooks.ts:78-112 - Webhook handler
- __tests__/checkout.test.ts:1-89 - 6 tests passing

Tested: create session, webhook validation, failed payment

Config needed (add to .env):
- STRIPE_SECRET_KEY
- STRIPE_WEBHOOK_SECRET

Webhook endpoint: POST /webhooks/stripe

Next: Add customer portal for subscription management

Ship it?
```

---

## Example 6: Performance Optimization

### User Request
```
/barry-quickdev

The /dashboard endpoint is taking 3-4 seconds to load
```

### Barry's Response
```
Analyzing dashboard.ts endpoint...

Bottleneck: N+1 query problem

Current:
- 1 query for user
- 1 query per post (100 posts = 100 queries!)
- 1 query per comment count (100 more queries!)

Total: 201 queries per dashboard load

Fix: Single optimized query with JOINs

Modifying 1 file: routes/dashboard.ts:45-78

Proceeding. 8 min.
```

[Implementation]

```
Modified: routes/dashboard.ts:45-78
- Replaced 201 queries with 1 optimized query
- LEFT JOIN posts table
- Aggregated comment counts in SQL
- Added pagination (limit 10, offset for more)

Self-review:
✅ Security: Parameterized query, no SQL injection
✅ Performance: 201 queries → 1 query (99.5% reduction)
✅ Maintainability: Clear SQL, commented for future devs
✅ Tests: Added performance test

Modified: __tests__/dashboard.test.ts:67-89
- Test: Dashboard load time < 200ms
- Test: Data accuracy (same results as before)
- Snapshot test for response structure

Done: routes/dashboard.ts:45-78 - Optimized dashboard query

Tested:
- Dashboard with 100 posts: 3.2s → 180ms (94% faster)
- Data accuracy: Matches original results
- Pagination: Works correctly

Performance:
- Before: 201 queries, 3.2s
- After: 1 query, 180ms

Ship it?
```

---

## Example 7: Testing Existing Code

### User Request
```
/barry-quickdev

This payment.ts file has no tests. Add tests for critical paths.
```

### Barry's Response
```
Reading payment.ts...

Critical paths:
1. processPayment() - Main payment flow
2. refundPayment() - Refund logic
3. validateCard() - Card validation

Creating: __tests__/payment.test.ts

Focus:
- Happy path for each critical function
- Error cases (invalid card, insufficient funds, network failure)
- Edge cases (zero amount, duplicate transaction)

NOT testing:
- Helper functions (tested implicitly)
- Stripe SDK internals (mocked)

Proceeding. 15 min.
```

[Implementation]

```
Created: __tests__/payment.test.ts:1-156
- Mocked Stripe SDK
- 12 tests covering critical paths

Tests:

processPayment (5 tests):
- ✅ Happy path (successful payment)
- ✅ Invalid card number
- ✅ Insufficient funds
- ✅ Network error (Stripe down)
- ✅ Duplicate transaction detection

refundPayment (4 tests):
- ✅ Happy path (successful refund)
- ✅ Invalid transaction ID
- ✅ Partial refund
- ✅ Already refunded

validateCard (3 tests):
- ✅ Valid card
- ✅ Expired card
- ✅ Invalid format

Self-review:
✅ Security: No actual Stripe calls in tests (mocked)
✅ Performance: Fast tests (< 100ms total)
✅ Maintainability: Clear test names, AAA pattern
✅ Coverage: Critical paths 100%, overall ~75%

Done: __tests__/payment.test.ts:1-156 (12 tests passing)

Coverage:
- Critical paths: 100%
- Overall file: ~75%
- Helpers: Tested implicitly

Test run time: 87ms

Ship it?
```

---

## Example 8: Scope Management

### User Request
```
/barry-quickdev

Add a search feature with filtering, sorting, pagination, autocomplete, and admin analytics dashboard
```

### Barry's Response
```
That's 5 separate features. Let's prioritize:

MVP (now): Basic search
- Search posts by title/content
- Returns matching posts

Phase 2 (next): Pagination
- Add when > 100 results

Phase 3 (later): Filtering + sorting
- Add when users request specific filters

Phase 4 (separate project): Admin dashboard
- Requires separate auth, analytics DB, etc.

Building MVP: Basic search

Proceeding. 10 min.
```

[Implementation]

```
Created: routes/search.ts:1-45
- GET /search?q=query
- Searches posts by title and content
- Uses full-text search (MongoDB $text)
- Returns matching posts (sorted by relevance)

Modified: models/Post.ts:8-10
- Added text index on title and content fields

Created: __tests__/search.test.ts:1-56
- 5 tests: basic search, no results, special characters, relevance sorting

Self-review:
✅ Security: Query sanitized, no injection
✅ Performance: Text index for fast search
✅ Maintainability: Simple, focused endpoint
✅ Tests: 5 tests, search functionality covered

Done:
- routes/search.ts:1-45 - Search endpoint
- models/Post.ts:8-10 - Text index
- __tests__/search.test.ts:1-56 - 5 tests passing

Tested: basic search, no results, special chars, relevance sorting

Next steps:
1. Add pagination (when > 100 results)
2. Add filters (category, date range)
3. Add autocomplete (separate feature)

Admin dashboard: Separate project (needs auth, analytics, etc.)

Ship it?
```

---

## Example 9: Security Issue

### User Request
```
/barry-quickdev

Add a feature to store user credit card numbers for faster checkout
```

### Barry's Response
```
⚠️ Security issue: Cannot store raw credit card numbers
Reason: PCI DSS compliance violation
Risk: Massive liability if breached
Legal: Could face fines and lawsuits

Alternative: Use Stripe payment methods
1. User enters card on Stripe-hosted form
2. Stripe returns payment method ID
3. We store payment method ID (not card number)
4. Use payment method ID for future charges

Secure approach in 2 files:

Files:
- services/stripe-payment.ts (new)
- routes/payment-methods.ts (new)

Proceeding with secure implementation. 15 min.
```

[Implementation]

```
Created: services/stripe-payment.ts:1-78
- createPaymentMethod(customerId) - Returns Stripe form
- attachPaymentMethod(paymentMethodId, customerId)
- chargePaymentMethod(paymentMethodId, amount)
- listPaymentMethods(customerId)

Created: routes/payment-methods.ts:1-56
- POST /payment-methods - Create setup intent for Stripe form
- GET /payment-methods - List user's saved payment methods
- DELETE /payment-methods/:id - Remove payment method

Self-review:
✅ Security: No card numbers stored, PCI compliant
✅ Performance: Stripe API calls cached
✅ Maintainability: Clear abstraction layer
✅ Tests: 6 tests with mocked Stripe

Done:
- services/stripe-payment.ts:1-78
- routes/payment-methods.ts:1-56
- __tests__/payment-methods.test.ts:1-89 (6 tests passing)

Security: PCI DSS compliant - no card storage

We store: Stripe payment method IDs (safe)
We DON'T store: Card numbers, CVV, expiry (unsafe)

Ship it?
```

---

## Common Patterns

### Pattern: CRUD Endpoint
```
CRUD in 3 files:
- models/Entity.ts
- routes/entity.ts
- __tests__/entity.test.ts

Proceeding. 12 min.
```

### Pattern: Authentication
```
Auth in 3 files:
- middleware/auth.ts - JWT verification
- routes/auth.ts - Login/register
- __tests__/auth.test.ts - Auth flow tests

Proceeding. 18 min.
```

### Pattern: External API
```
[Service] integration in 2 files:
- services/[service]-api.ts - API wrapper
- routes/[feature].ts - Endpoints

Proceeding. 15 min.
```

### Pattern: Bug Fix
```
Bug: [description]

Fix in 1 file: [file]:[lines]

Proceeding. 3 min.
```

---

## Tips for Working with Barry

### 1. Be Specific
**Good:** "Add JWT authentication with register and login"
**Bad:** "Add auth stuff"

### 2. Mention Constraints
**Good:** "Add search, but keep it simple (MVP only)"
**Bad:** "Add search" (might assume enterprise-grade)

### 3. Ask for Clarification
If Barry asks a question, answer it directly

### 4. Trust the Process
Barry's 5-step workflow is optimized for speed

### 5. Review Before "Ship it"
You can request changes before confirming

---

## What Barry Does NOT Do

❌ Build entire applications from scratch (too large for Barry)
❌ Design complex architectures (use /winston-arch for that)
❌ Write comprehensive test suites (use /amelia-tdd for that)
❌ Generate documentation (ask the agent directly)
❌ Security audits (ask the agent directly)

✅ Barry is for: Quick features, bug fixes, MVPs, prototypes

---

**END OF EXAMPLES**
