# Python

Apply this on top of [SKILL.md](../SKILL.md).

## Defaults

- Prefer explicit, readable Python over compact cleverness.
- Add type hints at public boundaries and important data shapes.
- Prefer `Path`, `@dataclass`, and standard library tools when they fit.
- Use `snake_case` for functions and variables, `PascalCase` for classes, `UPPER_SNAKE_CASE` for constants.
- Keep async flow linear with `async` / `await`.
- Never use mutable default arguments.

## Good direction

- `def find_user_by_email(email: str) -> User | None:`
- `source_path: Path`
- `tags: list[str] | None = None`

## Avoid

- Type prefixes in names like `str_email`
- Shared defaults like `tags=[]`
- Dense one-liners that hide the main path
