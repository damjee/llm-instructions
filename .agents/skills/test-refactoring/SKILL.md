---
name: test-refactoring
description: Guidelines for AAA test structure. Use when writing, refactoring, or reviewing tests.
---

# Test Refactoring

## Philosophy

**Core principle**: Test behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

**Good tests** exercise real code paths through public APIs and read like specifications: "user can checkout with valid cart." They describe _what_ the system does, not _how_ it's implemented, so internal refactors don't break them.

**Bad tests** couple to implementation: mocking internal collaborators, testing private methods, asserting call counts/order, or bypassing the public interface (e.g. querying a database directly). Warning sign: a refactor breaks tests without changing behavior.

## AAA: Arrange → Act → Assert

Visually distinguish sections; prefer blank lines followed by one-word comments (e.g. `--- Arrange ---`).

**Arrange**: All setup; initialize the SUT at the top, ideally on the first line with doubles. Hide arrangement in helpers only with explicit user authorization.

**Act**: Exactly one logical SUT action; ideally one line calling one SUT method and storing the result.

**Assert**: All required assertions; no other logic, setup, or SUT execution.

## Guidelines

### Conventions

Use the project's domain glossary for names and interface vocabulary; respect applicable ADRs. Prefer existing conventions; when in doubt, ask the user.

### Structure

- Code Layout: Variables and Types → Tests → Local Helpers
- Prefer happy path first, then explicit failure reasons
- Prefer guard clauses over nesting
- Give distinct invalid cases separate, named tests with visible inputs and expected outcomes.

### Naming

- Name the system under test clearly; prefer **sut** without local conventions.
- Tests: behavior, not implementation.
- Variables: what they ARE, not what they DO; intent, not implementation.
- Behavior: verbs; data: nouns; booleans: predicates; collections: plurals.
- Abbreviations only from the domain glossary.
- No type encoding.
- Prefer self-documenting code over comments, except AAA section delineators.

### Test Doubles

Prefer: Dummy → Stub → Fake → Spy → Mock

Spy/Mock at **system boundaries** only:

- External APIs (payment, email, etc.)
- Databases (sometimes - prefer test DB)
- Time/randomness
- File system (sometimes)

Don't spy/mock:

- Your own classes/modules
- Internal collaborators
- Anything you control

### Helpers

Minimal, logic-free, and useful across:

- Local: ≥3 tests
- Shared: ≥3 test files

## Code Smells

1. [ ] Nesting beyond 3 indents
2. [ ] Comments other than AAA section delineators
3. [ ] Excessive global variables
4. [ ] Excessive helpers
5. [ ] Code outside AAA sections
6. [ ] Conditional logic in tests or helpers
7. [ ] Higher-order test doubles than necessary
8. [ ] Test does not follow AAA pattern
9. [ ] AAA sections not visually distinct (e.g. blank lines within a section or missing between sections)
10. [ ] Code in the wrong AAA section
11. [ ] Multiple logical actions in Act
12. [ ] Test name describes HOW, not WHAT
13. [ ] Test variable names describe what they DO, not what they ARE

## Procedure

**Review-only**: Apply guidelines and smell checks without editing; report actionable findings with locations.

1. **New tests**: Apply conventions, run relevant tests, and report results and blocked/unavailable verification. If only adding tests, finish here; use steps 2–6 when refactoring existing tests.
2. **Passing tests**: Verify user approval to refactor before editing; ask if missing.
3. Establish a passing suite baseline before refactoring. If tests fail or cannot run, report the blocker and establish a working baseline before proceeding.
4. Resolve only the highest-priority smell, then rerun the entire suite. Fix or revert a failing change before continuing.
5. Restart the checklist from the top after each passing refactor.
6. When no actionable smells remain, rerun the entire suite. Resolve regressions and restart the checklist; finish with results and remaining blockers.
