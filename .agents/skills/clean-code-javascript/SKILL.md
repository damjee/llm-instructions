---
name: clean-code-javascript
description: Apply JavaScript-specific clean-code conventions when refactoring, fixing, or reviewing JavaScript code. Use with the clean-code core.
---

# Clean Code: JavaScript

Apply [Clean Code](../clean-code/SKILL.md) with the conventions below. Use its procedure and completion checks; load only accessories for languages in scope.

## Conventions

- Use `const` by default and `let` for reassignment; keep `var` out of new or refactored code.
- Prefer clear helpers over inline cleverness.
- Use strict equality and explicit checks, preserving valid `0`, `''`, and `false` values.
- Prefer linear `async` / `await` over chained promises.
- Use plain objects for simple records and classes for behavior-rich models.
- Keep module exports small and intentional; keep internal helpers private.
