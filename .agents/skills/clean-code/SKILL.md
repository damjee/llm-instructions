---
name: clean-code
description: Apply core clean-code principles during refactoring, bug fixes, and code review. Use for clean-code or SOLID requests; combine with the accessory for each language being changed.
---

# Clean Code

## Philosophy

Clean code is easy to read, easy to change, and consistent within a project.

## Procedure

1. Read the project's domain glossary and relevant ADRs. Match existing conventions; ask when a material choice remains unclear.
2. Establish a working solution before refactoring. For review-only work, identify findings without changing code.
3. Load the accessory for each language in scope:
   - [JavaScript](../clean-code-javascript/SKILL.md)
   - [TypeScript](../clean-code-typescript/SKILL.md)
   - [Python](../clean-code-python/SKILL.md)
   - [Godot / GDScript](../clean-code-gdscript/SKILL.md)
   - For other languages, apply this core with project conventions.
4. For writing or refactoring tests, also apply [Test Refactoring](../test-refactoring/SKILL.md). Its test layout and AAA comment rules specialize the production-code rules below.
5. Apply the guidelines and review each smell. For edits, verify behavior with the project's relevant checks; report results and any checks blocked or unavailable. For reviews, report actionable findings with locations.

## Structure

- Prefer Public API → Private API → Helpers.
- Give each code unit a clear, narrow responsibility.
- Prefer guard clauses over nesting.
- Pass dependencies as arguments rather than referencing them directly.
- Use inheritance to enforce an interface, not merely to remove duplication.
- Prefer deterministic functions with explicit side effects.

## Naming

- Reveal intent rather than implementation.
- Use verbs for behavior, nouns for data, and predicates for booleans.
- Name collections in plural form.
- Use full words or abbreviations defined in the domain glossary.
- Keep type encoding out of names.
- Prefer self-documenting code over comments.
- Replace magic numbers with named constants.

## Review Checklist

Review these signals in context; they prompt judgment rather than automatic rewrites.

- [ ] Layout differs from Public API → Private API → Helpers.
- [ ] A function exceeds 20 lines.
- [ ] Nesting exceeds 3 indents.
- [ ] A comment is present.
