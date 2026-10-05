---
name: javascript-guidelines
description: Apply JavaScript-specific clean-code conventions when refactoring, fixing, or reviewing JavaScript code.
---

# JavaScript Guidelines

## Conventions

- Prefer `const`. Reserve `let` for reassignment; never use `var`.
- Prefer clear helpers over inline cleverness.
- Use strict equality and explicit value checks. Avoid truthiness when `0`, `''`, or `false` are valid values.
- Prefer `async` / `await` over promise chains; avoid nested `.then()` pyramids.
- Use plain objects for simple records and classes for behavior-rich models.
- Keep the module's public API small and intentional; leave internal helpers unexported.
