---
name: clean-code-python
description: Apply Python-specific clean-code conventions when refactoring, fixing, or reviewing Python code.
---

# Clean Code: Python

## Conventions

- Prefer explicit, readable Python over compact cleverness. Avoid dense one-liners that hide the main path.
- Add type hints at public boundaries and for important data shapes.
- Prefer `Path`, `@dataclass`, and other standard-library tools when they fit.
- Use `snake_case` for functions and variables, `PascalCase` for classes, and `UPPER_SNAKE_CASE` for constants.
- Keep async flow linear with `async` / `await`.
- **Keep defaults immutable:** use defaults such as `tags: list[str] | None = None` and create mutable values inside the function.
