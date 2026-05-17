---
name: clean-code
description: Baseline clean-code guidance for refactoring, bug fixes, and code review. Use when the user wants to refactor code, review code, requests "clean code", or requests SOLID.
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

1. Each unit should have only one reason to change
2. Keep one level of abstraction per function
3. If a function grows beyond roughly 20 lines, review
4. Prefer guard clases over nesting
5. Prefer structure Public API → Private API → Helpers

### Naming Guidelines

1. Ensure names reveal intent
2. Verb-based names for behavior, nouns for data, booleans as predicates such as isReady, or canRetry
3. Name collections in plural form
4. Avoid abbreviations not in the domain glossary.
5. Avoid type encoding in names
6. Prefer self-documenting code over comments
7. Use named constants over magic numbers
