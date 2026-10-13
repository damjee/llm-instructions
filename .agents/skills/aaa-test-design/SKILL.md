---
name: aaa-test-design
description: Guidelines for AAA test structure. Use when writing, refactoring, or reviewing tests.
---

# AAA Test Design

## Philosophy

The AAA test structure produces reliably good tests. Tests should test behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

### Definitions

**Good tests** exercise real code paths through public APIs and read like specifications: "user can checkout with valid cart." They describe _what_ the system does, not _how_ it's implemented, so internal refactors don't break them.

**Bad tests** couple to implementation. They may mock internal collaborators, test private methods, assert call counts/order, or bypass the public interface. Warning sign: a refactor breaks tests without changing behavior.

## Structural Standards

- **API legibility**: Specify public contracts directly, not incidentally through other operations. Domain scenarios should clarify the API.
- **Explicit subject**: Identify the object, constructor, or function under test. Testing helpers does not establish production behavior.
- **Independent action**: Count meaningful operations, not statements. Chaining or batching independent actions does not make them one behavior.
- **Direct evidence**: Assert observable outcomes without reconstruction that hides defects. Test machinery must not become a second implementation.
- **Responsibility boundaries**: Complex setup, execution, or observation signals API friction. Investigate responsibilities; distinguish test complexity from suspected production defects.

Coverage volume cannot compensate for structural failures.

## Test Responsibilities

- Segregate test families into distinct modules. (I.E. unit tests vs integration tests)
- Unit tests should specify business logic through public behavior.
- Integration tests should focus on contracts with external systems, rather than repeat business-logic coverage.

## AAA: Arrange → Act → Assert

Visually distinguish sections; prefer blank lines followed by one-word comments (e.g. `--- Arrange ---`).

**Arrange**: Section for all setup and arrangement for the test; initialize the SUT at the top, ideally on the first line. Hide arrangement in helpers only with explicit user authorization.

**Act**: Exercises the SUT with exactly one logical SUT action; ideally one line calling one SUT method and storing the result if necessary.

**Assert**: All required assertions; no other logic, setup, or SUT execution.

## Guidelines

### Conventions

Use the project's domain glossary for names and interface vocabulary; respect applicable ADRs. Prefer existing conventions; when in doubt, ask the user.

### Structure

- Code Layout: Variables and Types → Tests → Local Helpers
- Tests ordered to prefer happy path first, then explicit failure reasons
- Prefer guard clauses over nesting
- Give distinct invalid cases separate, named tests with visible inputs and expected outcomes.

### Naming

- Name the system under test clearly; prefer **sut** when local conventions exist.
- Test behavior, not implementation.
- Variables: what they ARE, not what they DO; intent, not implementation.
- Behavior: verbs; data: nouns; booleans: predicates; collections: plurals.
- Abbreviations only from the domain glossary.
- No type encoding.
- Prefer self-documenting code over comments, except AAA section delineators.

### Test Doubles

When a test double is required, prefer them in this order:

Dummy → Stub → Fake → Spy → Mock

Always challenge if a double could be replaced by a more simple double (i.e. replace a Spy with a Fake or Stub)

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

Helpers should not hide test arrangement. Prefer code duplication unless local conventions or the user explicitly overrides this requirement. During review still flag the preference to not abstract test arrangement.

## Code Smells

Code smells indicate a problem **may** exist. You must review to determine if a real violation exists.

1. [ ] Nesting beyond 3 indents
2. [ ] Comments other than AAA section delineators
3. [ ] Excessive global variables or variables outside of test code
4. [ ] Helper functions inside the arrange section
5. [ ] Code outside AAA sections
6. [ ] Conditional logic in tests or helpers
7. [ ] Spies or Mock test doubles in code
8. [ ] Test does not follow AAA pattern
9. [ ] AAA sections not visually distinct (e.g. blank lines within a section or missing between sections)
10. [ ] Code in the wrong AAA section
11. [ ] Multiple logical actions in Act
12. [ ] Test name describes HOW, not WHAT
13. [ ] Test variable names describe what they DO, not what they ARE

## Assessment

Read names, then bodies. Assess structure, happy paths, failure contracts, then conventions. Investigate smells; do not hide complexity in helpers. State coverage limits when contracts are unavailable.

## Usage

- **Writing or refactoring**: Apply these standards within the chosen workflow. Make small authorized changes and validate them.
- **Reviewing**: Report structural failures, coverage gaps, then local issues. Do not edit or prescribe fixes.
