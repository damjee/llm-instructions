---
name: clean-code-typescript
description: Apply TypeScript-specific clean-code conventions when refactoring, fixing, or reviewing TypeScript code. Use with the clean-code core.
---

# Clean Code: TypeScript

Apply [Clean Code](../clean-code/SKILL.md) with the conventions below. Use its procedure and completion checks; load only accessories for languages in scope.

## Conventions

- Prefer precise shapes, unions, and literals over loose objects.
- Type exported boundaries explicitly.
- Use `unknown` with narrowing and validation for untrusted values; avoid `any` as an escape hatch.
- Prefer guards that prove a shape over casts such as `value as string`.
- Use `readonly` for stable inputs and data; use mutable parameter types when mutation is part of the design.
- Prefer `interface` for extensible contracts and `type` for unions and compositions.
- Keep async code linear and typed.
