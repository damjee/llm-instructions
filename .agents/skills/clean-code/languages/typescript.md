# TypeScript

Apply this on top of [SKILL.md](../SKILL.md).

## Defaults

- Make types do real work. Prefer precise shapes, unions, and literals over loose objects.
- Type exported boundaries explicitly.
- Prefer narrowing and validation over casting.
- Use `readonly` for stable inputs and data where mutation is not intended.
- Prefer `interface` for extensible contracts and `type` for unions and compositions.
- Keep async code linear and typed.

## Good direction

- `type PaymentResult = { status: 'success'; receiptId: string } | { status: 'failure'; reason: string }`
- `export function findUserByEmail(email: string): User | null`
- `function readRequestId(value: unknown): string`

## Avoid

- `any` as an escape hatch
- `value as string` when a guard can prove the shape
- Mutable parameter types unless mutation is part of the design
