# TypeScript Standards

**Purpose:** TypeScript-specific coding conventions and style guidance  
**Audience:** AI agents working with TypeScript codebases  
**Scope:** TypeScript language practices, static typing, and application structure conventions  
**When to Load:** Load when the repository or task uses TypeScript.  
**Load Order:** Load after Clean Code Standards; also load Testing Philosophy if tests or testability are in scope.  
**Skip If:** The task is language-agnostic or the project is not TypeScript.  
**Typical Tasks:** Implement TypeScript features, refactor typed modules, review TypeScript code, improve type safety, replace `any`, design typed APIs.

---

## Core Requirements

Apply these standards on top of [Clean Code Standards](../../clean-code-standards.md). This document adds **TypeScript-specific** conventions and starter preferences for this repository.

---

## Starter Preferences

### Make types do real work

- Encode important invariants in types when practical.
- Prefer specific object shapes, unions, and literal types over generic loose objects.
- Do not bypass the type system with `any` unless there is no viable alternative.

**✅ Good**

```typescript
type PaymentResult =
  | { status: 'success'; receiptId: string }
  | { status: 'failure'; reason: string };
```

**❌ Bad**

```typescript
type PaymentResult = any;
```

### Type public boundaries explicitly

- Add explicit parameter and return types to exported functions and important public methods.
- Let local inference reduce noise inside straightforward implementations.
- Keep type information in annotations, not variable names.

**✅ Good**

```typescript
export function findUserByEmail(email: string): User | null {
  return userRepository.findByEmail(email);
}
```

**❌ Bad**

```typescript
export function findUserByEmail(strEmail) {
  return userRepository.findByEmail(strEmail);
}
```

### Prefer narrowing over casting

- Use control flow, discriminated unions, and guards to narrow types.
- Prefer `unknown` over `any` for unchecked inputs, then validate.
- Avoid `as` casts when a guard or parser can prove the shape.

**✅ Good**

```typescript
function readRequestId(value: unknown): string {
  if (typeof value !== 'string') {
    throw new Error('request id must be a string');
  }

  return value;
}
```

**❌ Bad**

```typescript
function readRequestId(value: unknown): string {
  return value as string;
}
```

### Prefer `readonly` for stable data

- Mark fields, array inputs, and object properties `readonly` when callers should not mutate them.
- Use immutability to make state flow easier to reason about.
- Mutate only where the design genuinely requires it.

**✅ Good**

```typescript
type InvoiceLine = {
  readonly price: number;
  readonly quantity: number;
};

function calculateInvoiceTotal(lines: readonly InvoiceLine[]): number {
  return lines.reduce((total, line) => total + line.price * line.quantity, 0);
}
```

**❌ Bad**

```typescript
type InvoiceLine = {
  price: number;
  quantity: number;
};

function calculateInvoiceTotal(lines: InvoiceLine[]): number {
  return lines.reduce((total, line) => total + line.price * line.quantity, 0);
}
```

### Use `interface` for extensible contracts and `type` for compositions

- Prefer `interface` for object contracts meant to be implemented or extended.
- Prefer `type` for unions, mapped types, and type composition.
- Stay consistent within a file or module once one form is clearly the better fit.

**✅ Good**

```typescript
interface PaymentGateway {
  charge(amountInCents: number): Promise<PaymentResult>;
}

type PaymentStatus = 'pending' | 'paid' | 'failed';
```

**❌ Bad**

```typescript
type PaymentGateway = {
  charge: (amountInCents: number) => Promise<any>;
};
```

### Prefer `async` / `await` with typed results

- Keep async control flow linear and typed.
- Return domain-friendly result shapes instead of loosely typed response blobs.
- Surface failures through typed errors or explicit result types.

**✅ Good**

```typescript
export async function loadDashboard(userId: string): Promise<Dashboard> {
  const user = await userRepository.findById(userId);
  const notifications = await notificationService.loadForUser(userId);

  return { user, notifications };
}
```

**❌ Bad**

```typescript
export async function loadDashboard(userId) {
  const user = await userRepository.findById(userId);
  const notifications = await notificationService.loadForUser(userId);

  return { user, notifications } as any;
}
```

## Summary

- Make the type system carry real meaning.
- Type exported boundaries explicitly.
- Prefer narrowing and validation over casting.
- Use `readonly` for stable data and parameters.
- Use `interface` for extensible contracts and `type` for compositions.
- Keep async code linear and typed.
