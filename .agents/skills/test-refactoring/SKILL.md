---
name: test-refactoring
description: Guidance for how user prefers test code layout, naming, and structure. Use when writing or refactoring test code, or when the user requests "clean code" or "AAA" on a test file.
---

# Test Refactoring

## Philosophy

**Core principle**: Tests should verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

**Good tests** focus on observable behavior not how it's implemented. they exercise real code paths through public APIs. They describe _what_ the system does, not _how_ it does it. A good test reads like a specification - "user can checkout with valid cart" tells you exactly what capability exists. These tests survive refactors because they don't care about internal structure.

**Bad tests** are coupled to implementation. They mock internal collaborators, test private methods, assert on call counts/order, or verify through external means (like querying a database directly instead of using the interface). The warning sign: your test breaks when you refactor, but behavior hasn't changed.

**AAA Pattern**: Arrange → Act → Assert structure. A test structured in a way that setup happens first, followed by execution, and finally verification.

### AAA Definitions

**Arrange**: Should perform all test setup, but should not exercise the SUT or perform assertions. Doubling and SUT initialization should happen here.
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
5. [ ] Conditional logic is present in the test or helpers
6. [ ] Test uses higher-order test doubles than necessary
7. [ ] Test does not follow AAA pattern
8. [ ] AAA sections are not visually distinct, such as blank lines present within a section or missing between sections
9. [ ] Arrange, Act, or Assert sections contain code belonging to another section
10. [ ] Test has multiple logical actions in Act section
11. [ ] Test name describes HOW, not WHAT
12. [ ] Test variables names describe what they DO, not what they ARE

## Procedure

Refactor to resolve the only highest priority smell. Work incrementally to resolve a single smell at a time, do not attempt large patches to fix multiple issues at once.

If refactoring existing code, confirm all tests are passing before completing the refactor.

When the refactor is complete, retest for code smells starting at the top of the list. Every refactor may reveal new smells or issues, so continue iterating fresh each time.
