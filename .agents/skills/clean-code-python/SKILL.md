---
name: clean-code-python
description: Apply Python-specific clean-code conventions when refactoring, fixing, or reviewing Python code. Use with the clean-code core.
---

# Clean Code: Python

Apply [Clean Code](../clean-code/SKILL.md) with the conventions below. Use its procedure and completion checks; load only accessories for languages in scope.

## Conventions

- Prefer explicit, readable Python over compact cleverness; avoid dense one-liners that hide the main path.
- Add type hints at public boundaries and important data shapes.
- Prefer `Path`, `@dataclass`, and standard-library tools when they fit.
- Use `snake_case` for functions and variables, `PascalCase` for classes, and `UPPER_SNAKE_CASE` for constants.
- Keep async flow linear with `async` / `await`.
- Use immutable defaults, such as `tags: list[str] | None = None`, and create mutable values inside the function.
