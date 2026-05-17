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
- Keep one level of abstraction per function
- If a function grows beyond roughly 20 lines, check whether distinct concerns should be extracted.

### 2. Prefer guard clauses over nesting

- Prefer the happy path at the lowest indentation level.
- Do not carry placeholder variables only to return them later.

### 3. Make names reveal intent

- Verb-based names for behavior, nouns for data, booleans as predicates such as isReady, or canRetry.
- Name collections in plural form.
- Avoid abbreviations not in the domain glossary.

### 4. Avoid type encoding in names
### 5. Prefer self-documenting code over comments
### 6. Prefer consistent structure

- Prefer the structure Public API → Private API → Helpers
- Group related behavior together.
- Avoid unnecessary indirection.
- Use top-level abstractions when they clarify intent.
