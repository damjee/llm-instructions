---
name: rust-guidelines
description: Apply Rust-specific clean-code conventions when writing, refactoring, fixing, or reviewing Rust code.
---

# Rust Guidelines

## Conventions

- Use `rustfmt`; honor project configuration, otherwise use defaults.
- Use `snake_case` for modules, functions, and variables; `UpperCamelCase` for types, traits, and variants; `SCREAMING_SNAKE_CASE` for constants and statics.
- Choose iterator chains or loops for clear control flow.
- Run configured Clippy checks; resolve findings or justify exceptions. Select additional lints deliberately.

## Ownership and Types

- **Make ownership intentional:** prefer borrowing non-`Copy` inputs when ownership isn't needed; otherwise accept owned values instead of cloning internally.
- Prefer `&str` and `&[T]` over `&String` and `&Vec<T>` when container-specific features are unnecessary.
- **Make types carry meaning:** use `Option` for absence, enums for alternatives, and newtypes for distinct domain values.
- Prefer standard traits; derive when semantics fit. Implement `From` for obvious, lossless, value-preserving, infallible conversions; `TryFrom` for fallible conversions.

## API Contracts

- Use `Result` for expected failures; handle errors or propagate with `?`. Explain the invariant behind each `expect`.
- Keep visibility narrow; protect invariants with private fields.
- Document public contracts and relevant errors, panics, and safety requirements. Justify each `unsafe` block's soundness.

## Sources

- [Rust Style Guide](https://doc.rust-lang.org/style-guide/)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [Clippy](https://doc.rust-lang.org/clippy/usage.html)
