---
name: clean-code-typescript
description: Apply TypeScript-specific clean-code conventions when refactoring, fixing, or reviewing TypeScript code. Use with the clean-code core.
---

# Clean Code: TypeScript

Apply [Clean Code](../clean-code/SKILL.md) with these conventions.

## Types and Contracts

- **Make types carry meaning:** prefer precise shapes, unions, and literals over loose objects.
- Type exported boundaries explicitly.
- Use `unknown` for untrusted values, then narrow and validate. Avoid `any` as an escape hatch.
- Prefer guards that prove a shape over casts such as `value as string`.
- Use `readonly` for stable inputs and data; use mutable parameter types when mutation is part of the design.
- Prefer `interface` for extensible contracts and `type` for unions and compositions.
- Keep async code linear and typed.
