---
name: test-refactoring
description: Guidance for how user prefers test code layout, naming, and structure. Use when writing or refactoring test code, or when the user requests "clean code" or "AAA" on a test file.
---

# Test Refactoring

Apply [Clean Code](../clean-code/SKILL.md) and the accessory for the language in scope. The test-specific rules below specialize its production-code layout and comment guidance.

## Philosophy

**Core principle**: Tests should verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

**Good tests** focus on observable behavior not how it's implemented. they exercise real code paths through public APIs. They describe _what_ the system does, not _how_ it does it. A good test reads like a specification - "user can checkout with valid cart" tells you exactly what capability exists. These tests survive refactors because they don't care about internal structure.

**Bad tests** are coupled to implementation. They mock internal collaborators, test private methods, assert on call counts/order, or verify through external means (like querying a database directly instead of using the interface). The warning sign: your test breaks when you refactor, but behavior hasn't changed.

**AAA Pattern**: Arrange → Act → Assert structure. A test structured in a way that setup happens first, followed by execution, and finally verification.

### AAA Definitions

**Arrange**: Should perform all test setup. Doubling and SUT initialization should happen here, ideally as the first line. Do not hide arrangement activity behind a test helper unless the user explicitly authorizes it.

**Act**: Should exercise one and only one logical action of the SUT. Ideally, should be a single line of code where the SUT makes a single method call and stores the result.

**Assert**: Should contain all assertions required but no other logic, setup, or execution of the SUT.

## Guidelines

### Implementation Guidelines

When exploring the codebase, use the project's domain glossary so that names and interface vocabulary match the project's language, and respect ADRs in the area you're touching.

Prefer consistency with the existing codebase over introducing new patterns. When in doubt, ask the user.

Verify with the user before refactoring passing tests.

### Structural Guidelines

1. Code Layout: Variables and Types → Tests → Local Helpers
2. Test Layout: Arrange → Act → Assert structure (AAA pattern)
3. Initialize sut at the top of Arrange
4. One logical action in Act
5. Prefer happy path first then explicit failure reasons
6. Arrange, Act, Assert sections should be visually distinct. Prefer blankline followed by a one word comment break (I.E. : --- Arrange --- ) to denote sections,
7. Prefer guard clauses over nesting
8. Give distinct invalid cases separate, named tests with visible inputs and expected outcomes.

### Naming Guidelines

1. The system under test should be clear. **sut** is the preferred name if no local conventions exist.
2. Test names should describe behavior, not implementation.
3. Test variables should describe what they ARE, not what they DO. Ensure names reveal intent, not implementation
4. Verb-based names for behavior, nouns for data, booleans as predicates
5. Name collections in plural form
6. Avoid abbreviations not in the domain glossary
7. Avoid type encoding in names
8. Prefer self-documenting code over comments. Exception for AAA section delineators.

### Mocking and Test Doubles

Prefer test doubles in this order: Dummy → Stub → Fake → Spy → Mock

Use _Spies_ and _Mock_ at **system boundaries** only:

- External APIs (payment, email, etc.)
- Databases (sometimes - prefer test DB)
- Time/randomness
- File system (sometimes)

Don't spy/mock:

- Your own classes/modules
- Internal collaborators
- Anything you control

### Helpers

Helpers should be minimal, contain no logic, and be useful across minimum 3 tests for local helpers, and 3 test files for shared helpers.

## Code Smells

1. [ ] Nesting beyond 3 indents
2. [ ] Comment is present other than AAA section delineators
3. [ ] Test has excessive global variables
4. [ ] Test has excessive helpers
5. [ ] Test had code outside of AAA sections
6. [ ] Conditional logic is present in the test or helpers
7. [ ] Test uses higher-order test doubles than necessary
8. [ ] Test does not follow AAA pattern
9. [ ] AAA sections are not visually distinct, such as blank lines present within a section or missing between sections
10. [ ] Arrange, Act, or Assert sections contain code belonging to another section
11. [ ] Test has multiple logical actions in Act section
12. [ ] Test name describes HOW, not WHAT
13. [ ] Test variables names describe what they DO, not what they ARE

## Procedure

1. For new tests, apply the conventions above and run the relevant tests. Report their results and any verification blocked or unavailable. If only adding new tests, finish here; use steps 2–6 when refactoring existing tests.
2. For existing passing tests, verify the user has approved refactoring them before editing; ask if approval is missing.
3. Establish a passing test-suite baseline before refactoring. If tests fail or cannot run, report the blocker and establish a working baseline before proceeding.
4. Resolve only the highest-priority smell, then rerun the entire test suite. If a change causes failures, fix or revert it before continuing.
5. Restart the checklist from the top after each passing refactor.
6. Once no actionable smells remain, rerun the entire suite. Resolve regressions and restart the checklist; finish with the test results and any remaining blockers.
