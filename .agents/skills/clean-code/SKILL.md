---
name: clean-code
description: Baseline clean-code guidance for refactoring, bug fixes, and code review. Use when the user wants to refactor code, review code, or requests "clean code"/SOLID.
---

# Clean Code

## Philosophy

**Core Principle**: Code should be easy to read, easy to change, and consistent within a project.

**Clean code** is easy to read, understand, and modify.

**Dirty code** is difficult to change without touching multiple units.

See [languages/](./languages/) for language-specific style guides.

## Implementation Guide

When exploring the codebase, use the project's domain glossary so that names and interface vocabulary match the project's language, and respect ADRs in the area you're touching.

Prefer consistency with the existing codebase over introducing new patterns. When in doubt, ask the user.

Do not apply clean code until you have a working solution.

## Clean Code Guidelines

### 1. Keep units small and focused

- Give each function, method, and class one clear responsibility.
- Keep one level of abstraction per function.
- Split validation, orchestration, transformation, and I/O when they compete for attention.
- If a function grows beyond roughly 20 lines, check whether distinct concerns should be extracted.

### 2. Prefer guard clauses over nesting

- Check invalid or edge cases first.
- Keep the happy path at the lowest indentation level.
- Prefer early returns over nested conditionals.
- Do not carry placeholder variables only to return them later.

### 3. Make names reveal intent

- Use verb-based names for behavior.
- Use descriptive nouns for data.
- Avoid abbreviations unless they are in the domain glossary.
- Name booleans as predicates such as is_ready, has_permission, or can_retry.
- Name collections in plural form.

### 4. Avoid type encoding in names

- Avoid Hungarian notation, type prefixes, and suffixes.
- In languages with optional typing, use the type system.

### 5. Prefer self-documenting code

- Do not use comments to explain WHAT the code is doing.
- Use naming so the code is self-explanatory.
- Replace magic numbers with named constants.
- Break complex logic into named steps.
- Reserve comments for public API docs or unavoidable external constraints.

### 6. Prefer consistent structure

- Prefer the structure Public API → Private API → Helpers
- Group related behavior together.
- Avoid unnecessary indirection.
- Use top-level abstractions when they clarify intent.
