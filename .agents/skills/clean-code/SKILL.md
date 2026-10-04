---
name: clean-code
description: Apply core clean-code principles during refactoring, bug fixes, and code review. Use for clean-code or SOLID requests; combine with the accessory for each language in scope.
---

# Clean Code

Clean code is easy to read, easy to change, and consistent within a project.

Use the project's domain vocabulary and respect ADRs. Prefer consistency with the existing codebase over introducing new patterns; when in doubt, ask the user.

## Workflow

1. **Load companion guidance** for each language in scope:
   - [JavaScript](../clean-code-javascript/SKILL.md)
   - [TypeScript](../clean-code-typescript/SKILL.md)
   - [Python](../clean-code-python/SKILL.md)
   - [Godot / GDScript](../clean-code-gdscript/SKILL.md)
   - Other languages: use this core with project conventions.

   For writing, refactoring, or reviewing tests, also use [Test Refactoring](../test-refactoring/SKILL.md). Its layout and AAA comment rules specialize the production-code rules below. For review-only work, use its guidance and smell checks without entering its refactoring procedure.

2. **For edits, make it work, then make it clean.** Apply the guidelines and review every smell. For review-only work, identify findings without changing code.

3. **Verify and report.** After edits, verify behavior with relevant project checks; report results and blocked or unavailable checks. For reviews, report actionable findings with locations.

## Structure

- Prefer Public API → Private API → Helpers.
- Give each code unit a clear, narrow responsibility.
- Prefer guard clauses over nesting.
- Prefer passing dependencies as arguments over direct references.
- Use inheritance to enforce an interface, not merely to remove duplication.
- Prefer deterministic functions with explicit side effects.

## Naming

- Reveal intent, not implementation.
- Use verbs for behavior, nouns for data, and predicates for booleans.
- Name collections in plural form.
- Use full words or abbreviations defined in the domain glossary.
- Keep type encoding out of names.
- Prefer self-documenting code over comments.
- Replace magic numbers with named constants.

## Code Smells Requiring Review

Review every signal in context; none requires an automatic rewrite.

- [ ] Layout differs from Public API → Private API → Helpers.
- [ ] A function exceeds 20 lines.
- [ ] Nesting exceeds 3 indents.
- [ ] A comment is present.
