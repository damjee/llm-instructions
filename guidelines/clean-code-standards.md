# Clean Code Standards

**Purpose:** Language-agnostic clean code rules for writing and improving code  
**Scope:** General principles that should apply across programming languages and projects  
**Related Documents:** [Godot & GDScript Standards](./languages/godot/gdscript-standards.md), [Testing Philosophy](./testing-philosophy.md)
**When to Load:** Load for any task that changes, reviews, or designs source code.  
**Load Order:** Load this first; it is the baseline for all other guides.  
**Skip If:** The task is unrelated to code changes or code quality.  
**Typical Tasks:** New implementation, refactoring, code review, bug fixing, API design.

---

## Primary Goal

Produce code that is readable, maintainable, consistent, and safe to change.

## Operating Defaults

- Preserve behavior while refactoring.
- Prefer clarity over cleverness.
- Follow the local project and language conventions consistently.
- Optimize for the next real change, not hypothetical future work.
- Make intent obvious at the point of use.

## Non-Negotiable Rules

### 1. Keep units small and focused

- Give each function, method, and class one clear responsibility.
- Keep one level of abstraction per function.
- Split validation, orchestration, transformation, and I/O when they compete for attention.
- If a function grows beyond roughly 20 lines, check whether distinct concerns should be extracted.

### 2. Use guard clauses instead of deep nesting

- Check invalid or edge cases first.
- Keep the happy path at the lowest indentation level.
- Prefer early returns over nested conditionals.
- Do not carry placeholder variables only to return them later.

### 3. Make names reveal intent

- Use verb-based names for behavior.
- Use descriptive nouns for data.
- Avoid abbreviations unless they are universally understood in the domain.
- Name booleans as predicates such as `is_ready`, `has_permission`, or `can_retry`.
- Name collections in plural form.

### 4. Put type information in the type system

- Names describe purpose, not representation.
- Do not use Hungarian notation or type prefixes/suffixes.
- When the language supports typing, express type information in annotations or declarations.

### 5. Prefer self-documenting code over explanatory comments

- Use well-named functions, variables, parameters, and constants.
- Replace magic numbers with named constants.
- Break complex logic into named steps.
- Do not use comments to compensate for unclear code.
- Reserve comments for public API docs or unavoidable external constraints.

### 6. Keep structure predictable

- Group related behavior together.
- Use consistent ordering and formatting within the project or language style guide.
- Keep public entrypoints easy to find and private helpers secondary.
- Avoid unnecessary indirection.

## Design Heuristics

- **SOLID:** separate responsibilities, extend safely, substitute implementations, keep interfaces focused, and depend on abstractions.
- **KISS:** choose the simplest solution that solves the current problem.
- **YAGNI:** do not build speculative features.
- **DRY:** remove true duplication once a pattern is proven, not before.
- **Tell, Don't Ask:** keep behavior close to the data it acts on.

## Refactoring Priorities

When cleaning existing code, fix issues in this order:

1. Deep nesting
2. Mixed responsibilities
3. Vague names
4. Magic numbers
5. Explanatory comments hiding unclear code

## Quick Adherence Check

Before finishing a change, verify:

- Is the main path easy to follow?
- Does every important name reveal intent?
- Are types expressed through the language rather than the identifier?
- Are repeated values or rules named?
- Can a reader understand the code without explanatory comments?
- Did the change preserve behavior?

## Summary

- Keep code small, clear, and focused.
- Prefer guard clauses over nesting.
- Make names do the explaining.
- Put type info in the type system.
- Use comments sparingly.
- Refactor toward simpler structure, not more machinery.
