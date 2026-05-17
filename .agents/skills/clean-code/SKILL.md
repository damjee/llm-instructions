---
name: clean-code
description: Baseline clean-code guidance for refactoring, bug fixes, and code review. Use when the user wants to refactor code, review code, or requests "clean code" or SOLID.
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

### Structural Guidelines

1. Prefer structure Public API → Private API → Helpers
2. Code units should have a clear and narrow responsibility
3. Prefer guard clauses over nesting
4. Prefer passing dependencies as arguments over creating or calling them
5. Use inheritance to enforce an interface; do not use it just to avoid code duplication
6. Prefer deterministic functions free from side effects

### Naming Guidelines

1. Ensure names reveal intent, not implementation
2. Verb-based names for behavior, nouns for data, booleans as predicates such as isReady, or canRetry
3. Name collections in plural form
4. Avoid abbreviations not in the domain glossary
5. Avoid type encoding in names
6. Prefer self-documenting code over comments
7. Use named constants over magic numbers

## Code Smells Requiring Review

- [ ] Function beyond 20 lines

- [ ] Nesting beyond 3 indents

- [ ] Comment is present
