# JavaScript Standards

**Purpose:** JavaScript-specific coding conventions and style guidance  
**Audience:** AI agents working with JavaScript codebases  
**Scope:** JavaScript language practices, structure, and browser or Node.js coding conventions  
**When to Load:** Load when the repository or task uses JavaScript.  
**Load Order:** Load after Clean Code Standards; also load Testing Philosophy if tests or testability are in scope.  
**Skip If:** The task is language-agnostic or the project is not JavaScript.  
**Typical Tasks:** Implement JavaScript features, refactor JavaScript modules, review JavaScript code, improve async flow, modernize runtime patterns.

---

## Core Requirements

Apply these standards on top of [Clean Code Standards](../../clean-code-standards.md). This document adds **JavaScript-specific** conventions and starter preferences for this repository.

---

## Starter Preferences

### Prefer `const` by default

- Use `const` unless reassignment is required.
- Use `let` when a value must change.
- Do not use `var`.

**✅ Good**

```javascript
const subtotal = calculateSubtotal(items);
let retriesRemaining = 3;
```

**❌ Bad**

```javascript
var subtotal = calculateSubtotal(items);
var retriesRemaining = 3;
```

### Prefer clear functions over inline cleverness

- Extract named helpers when expressions become hard to read.
- Keep side effects obvious.
- Prefer early returns and flat control flow.

**✅ Good**

```javascript
function calculateDiscountedTotal(cart) {
  if (!cart.hasDiscount) {
    return cart.subtotal;
  }

  return cart.subtotal - cart.discountAmount;
}
```

**❌ Bad**

```javascript
function discountedTotal(cart) {
  return cart.hasDiscount ? cart.subtotal - cart.discountAmount : cart.subtotal;
}
```

### Use strict equality and explicit checks

- Prefer `===` and `!==`.
- Be deliberate with truthiness checks when `0`, `''`, or `false` are valid values.
- Distinguish `null` / `undefined` handling from general falsy handling.

**✅ Good**

```javascript
function findPageSize(query) {
  if (query.pageSize === undefined) {
    return DEFAULT_PAGE_SIZE;
  }

  return query.pageSize;
}
```

**❌ Bad**

```javascript
function findPageSize(query) {
  if (!query.pageSize) {
    return DEFAULT_PAGE_SIZE;
  }

  return query.pageSize;
}
```

### Prefer `async` / `await` over chained promises

- Keep async code linear and easy to read.
- Wrap meaningful failure handling around awaited steps rather than burying logic in `.then()` chains.
- Use promise combinators intentionally when concurrency matters.

**✅ Good**

```javascript
async function loadUserProfile(userId) {
  const user = await userRepository.findById(userId);
  const posts = await postRepository.findByAuthor(userId);

  return { user, posts };
}
```

**❌ Bad**

```javascript
function loadUserProfile(userId) {
  return userRepository.findById(userId).then((user) =>
    postRepository.findByAuthor(userId).then((posts) => ({ user, posts })),
  );
}
```

### Prefer plain objects for records and classes for behavior-rich models

- Use plain objects for lightweight data shaping.
- Introduce classes when behavior, lifecycle, or invariants justify them.
- Avoid class syntax for passive data containers with no meaningful behavior.

**✅ Good**

```javascript
function toUserSummary(user) {
  return {
    id: user.id,
    displayName: user.displayName,
  };
}
```

**❌ Bad**

```javascript
class UserSummary {
  constructor(user) {
    this.id = user.id;
    this.displayName = user.displayName;
  }
}
```

### Keep module exports intentional

- Export a small public surface.
- Keep private helpers unexported.
- Prefer named exports when they improve clarity and discoverability.

**✅ Good**

```javascript
export function calculateInvoiceTotal(invoice) {
  return invoice.items.reduce((total, item) => total + item.price, 0);
}

function validateInvoice(invoice) {
  if (invoice.items.length === 0) {
    throw new Error('invoice must contain at least one item');
  }
}
```

**❌ Bad**

```javascript
export function calculateInvoiceTotal(invoice) {
  return invoice.items.reduce((total, item) => total + item.price, 0);
}

export function validateInvoice(invoice) {
  if (invoice.items.length === 0) {
    throw new Error('invoice must contain at least one item');
  }
}
```

## Summary

- Use `const` by default and never use `var`.
- Prefer readable helpers over compact cleverness.
- Use strict equality and explicit checks.
- Prefer `async` / `await` over promise chains.
- Use plain objects for simple records and classes for real behavior.
- Keep module exports small and intentional.
