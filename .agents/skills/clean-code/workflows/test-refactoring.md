# Test Refactoring

## Useage

Use this worklow when refactoring test files. This workflow is complementary to the [Clean Code Guidelines](../SKILL.md) and should be used in conjunction with them.

## Philosophy

**Core principle**: Tests should verify behavior through public interfaces, not implementation details. Code can change entirely; tests shouldn't.

**Good tests** focus on observable behavior not how it's implemented. they exercise real code paths through public APIs. They describe _what_ the system does, not _how_ it does it. A good test reads like a specification - "user can checkout with valid cart" tells you exactly what capability exists. These tests survive refactors because they don't care about internal structure.

**Bad tests** are coupled to implementation. They mock internal collaborators, test private methods, assert on call counts/order, or verify through external means (like querying a database directly instead of using the interface). The warning sign: your test breaks when you refactor, but behavior hasn't changed.

### Layout

- Code Layout: Variables and Types → Tests → Local Helpers
- Test Layout: Arrange → Act → Assert structure (AAA pattern)
  - Prefer sut initialization at the top of Arrange
  - Prefer one logical action in Act
  - Prefer happy path first then explicit failure reasons
- Arrange, Act, Assert sections should be visually distinct.
  - Prefer blank lines between sections

#### Arrange

**Should have**: Data setup, double initialization, sut initialization
**Should NOT have**: Result setup, assertions, execution of sut

#### Act

**Should have**: execution of sut
**Should NOT have**: Setup, initialization, assertions

#### Assert

**Should have**: result initialization, assertions
**Should NOT have**: Setup, initialization, execution of sut

### Naming

- The system under test should be clear. **sut** is the preferred name if no local conventions exist.
- Test names should describe behavior, not implementation.
- Test variables should describe what they ARE, not what they DO.

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

## Workflow

### 1. Planning

When exploring the codebase, use the project's domain glossary so that test names and interface vocabulary match the project's language, and respect ADRs in the area you're touching.

Before writing any code confirm all tests are passing.

### 2. Identify Smells

Identify any of the following smells in the test file. If one is present, fix it and rerun the workflow from the start before making any other changes. Prefer fixing smells in the order they are listed.

1. [ ] Test has excessive global variables
2. [ ] Test has excessive helpers
3. [ ] Conditional logic is present in the test or helpers
4. [ ] Test uses higher-order test doulbles than necessary
5. [ ] Test does not follow AAA pattern
6. [ ] AAA sections are not visually distinct, such as blank lines present within a section or missing between sections
7. [ ] Arrange, Act, or Assert sections contain code belonging to another section
8. [ ] Test has multiple logical actions in Act section
9. [ ] Test name describes HOW, not WHAT
10. [ ] Test variables names describe what they DO, not what they ARE

### 3. Refactor

Refactor to resolve the only highest priority smell. Work incrementally to resolve a single smell at a time, do not attempt large patches to fix multiple issues at once.

After each refactor, confirm all tests are passing before completing the refactor.

When the refactor is complete, rerun the workflow from the start. Every refactor may reveal new smells or issues, so continue iterating fresh each time.

### 4. Test

After review finds no additional smells, rerun the entire test suite to confirm all tests are passing. If any test fails, fix the issue and rerun the workflow from the start.
