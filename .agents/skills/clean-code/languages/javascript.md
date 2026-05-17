# JavaScript

Apply this on top of [SKILL.md](../SKILL.md).

## Defaults

- Prefer `const` by default. Use `let` only when reassignment is real. Never use `var`.
- Prefer clear helpers over inline cleverness.
- Use strict equality and explicit checks.
- Prefer `async` / `await` over chained promises.
- Use plain objects for simple records and classes for behavior-rich models.
- Keep module exports small and intentional.

## Good direction

- `const subtotal = calculateSubtotal(items);`
- `if (query.pageSize === undefined) { ... }`
- `export async function loadDashboard(userId) { ... }`

## Avoid

- Promise pyramids in `.then()`
- Truthiness checks when `0`, `''`, or `false` are valid values
- Exporting helpers that should stay private
