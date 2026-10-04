---
name: clean-code
description: Apply clean-code principles during refactoring, bug fixes, and code review. Use for clean-code or SOLID requests.
---

# Clean Code

Clean code is easy to read, easy to change, and consistent within a project.

Use the project's domain vocabulary and respect ADRs. Prefer consistency with the existing codebase over introducing new patterns; when in doubt, ask the user.

## Workflow

1. **For edits, make it work, then make it clean.** Apply the guidelines and review every smell. For review-only work, identify findings without changing code.

2. **Verify and report.** After edits, verify behavior with relevant project checks; report results and blocked or unavailable checks. For reviews, report actionable findings with locations.

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
