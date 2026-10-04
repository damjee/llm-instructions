---
name: clean-code
description: Guide for applying clean-code principles. Use when writing code, refactoring, or during code reviews. Use for clean code or SOLID requests.
---

# Philosophy

Clean code is easy to read, easy to change, and consistent within a project.

## Workflows

Use the project's domain vocabulary and respect ADRs. Prefer consistency with the existing codebase over introducing new patterns; when in doubt, ask the user.

### Writing

*Make it work, then make it clean.* Apply when no code exists.

1. Write a working version of the code disregarding the principles of this guide
2. Move into the refactoring workflow once the code is working

### Refactoring

Work in small atomic increments, iterating, and working on only one issue or smell at a time.

1. Identify an issue or code smell that needs review
2. Verify that a fix is appropriate
3. Apply the fix
4. Verify the code still works.
5. Return to 1 and identify the next issue or smell. Each edit may surface new issues or smells so review fresh each time.

### Code Review

1. Review the code for conflicts with the guide
2. Investigate any code smells and determine if they require fixes
3. Identify which findings are most critical to address. Problems with structure are usually more critical than naming.
4. Concisely report findings, prioritizing actionable critical findings.

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

Code smells indicate a problem **may** exist. You must investigate to determine if a violation exists.

- [ ] Layout differs from Public API → Private API → Helpers.
- [ ] A function exceeds 20 lines.
- [ ] Nesting exceeds 3 indents.
- [ ] A comment is present.
