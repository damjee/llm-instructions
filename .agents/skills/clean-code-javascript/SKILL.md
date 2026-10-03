---
name: clean-code-javascript
description: Apply JavaScript-specific clean-code conventions when refactoring, fixing, or reviewing JavaScript code. Use with the clean-code core.
---

# Clean Code: JavaScript

Apply [Clean Code](../clean-code/SKILL.md) with the conventions below. Use its procedure and completion checks; load only accessories for languages in scope.

## Conventions

- Prefer `const` by default. Use `let` only when reassignment is real. Never use `var`.
- Prefer clear helpers over inline cleverness.
- Use strict equality and explicit checks. Avoid truthiness checks when `0`, `''`, or `false` are valid values.
- Prefer `async` / `await` over chained promises; avoid promise pyramids in `.then()`.
- Use plain objects for simple records and classes for behavior-rich models.
- Keep module exports small and intentional; keep internal helpers private.
