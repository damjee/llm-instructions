# Testing Philosophy: Behavior-Focused Testing

**Purpose:** Testing philosophy and practices for maintaining code quality through refactor-safe tests  
**Scope:** Testing strategy, test design, and quality principles  
**Related Documents:** [Clean Code Standards](./clean-code-standards.md), [Godot & GDScript Standards](./languages/godot/gdscript-standards.md)
**When to Load:** Load when writing, changing, debugging, or evaluating tests, or when testability is part of the task.  
**Load Order:** Load after Clean Code Standards; use alongside language-specific guidance when relevant.  
**Skip If:** No tests are involved and the task does not affect test behavior or testability.  
**Typical Tasks:** Add tests, fix failing tests, review test quality, improve testability, define testing strategy.

---

## Primary Goal

Use tests to protect behavior and make refactoring safe.

**Golden Rule:** If a change preserves the public behavior, the tests should continue to pass.

## Operating Defaults

- Test public contracts and caller-visible behavior.
- Prefer tests that are fast, independent, repeatable, self-validating, and timely.
- Treat tests as executable specifications of behavior.
- Prefer real behavior over synthetic test machinery when practical.
- Optimize for confidence, not coverage percentages.

## Non-Negotiable Rules

### 1. Test behavior, not implementation

- Assert on public outputs, public errors, observable state, and caller-visible side effects.
- Do not assert on private state, helper calls, call counts, or call order unless that interaction is itself the contract.
- If implementation changes without changing behavior, the test should still pass.

### 2. Keep each test focused on one behavior

- A test should protect one unit of behavior.
- Use Arrange -> Act -> Assert as the default shape.
- Usually keep one logical action in the Act section.
- Multiple assertions are acceptable when they diagnose different aspects of the same behavior.
- If a test needs multiple act/assert cycles, it probably covers too much.

### 3. Name scenarios, not mechanics

- Test names should read like behavior descriptions in plain language.
- Prefer names that still make sense after refactoring.
- Avoid rigid templates that force implementation-first thinking.
- The test name should tell what broke, not which method happened to run.

### 4. Keep setup visible and local

- Make the relevant setup easy to see in the test.
- Prefer small factory or helper functions over broad shared setup.
- Avoid hiding scenario-defining state in constructors, `setUp`, `before_each`, or global fixtures.
- Accept some duplication when it keeps the scenario obvious.

### 5. Favor determinism

- The same test should produce the same result in any environment.
- Avoid conditional logic inside tests.
- Control time, randomness, ordering, and external dependencies.
- Prefer explicit return values, state changes, and errors over implicit side effects.

### 6. Prefer assertions in this order

1. **Output-based** — assert on return values or produced output; prefer this for pure logic and calculations
2. **State-based** — assert on caller-observable state changes; use this when resulting state is part of the contract
3. **Communication-based** — assert on collaborator interactions only when the interaction is the promised behavior; reserve this mainly for boundary behaviors

Use the lowest-brittleness style that still validates the contract.

### 7. Prefer stub-family doubles over mock-family doubles

- Use **dummies** (unused placeholders), **stubs** (canned answers), and **fakes** (working simplified replacements) when collaborators only provide indirect input into the scenario.
- Use **spies** (recorded interactions) or **mocks** (interaction expectations) only when the outgoing interaction is itself part of the contract.
- If a test only needs canned data, do not also assert interactions.
- Prefer sociable tests with real collaborators where practical, but isolate when isolation clarifies the contract.

### 8. Keep integration tests few and boundary-focused

- Business logic should be unit-test dominant.
- Use integration tests for adapters, persistence, messaging, HTTP, file systems, and other external boundaries.
- Integration tests validate boundary contracts; they do not replace lower-level tests.

### 9. Favor clarity over abstraction in tests

- Prefer explicit, self-contained tests over clever test abstractions.
- Apply the Rule of Three before extracting common test setup or helpers.
- Use parameterized tests only when the behavior is truly identical across cases.
- Keep important scenarios as explicit named tests when that improves diagnosis.

### 10. Preserve signal over metrics

- Prefer explicit assertions over snapshot or golden-style approval tests.
- Treat coverage as a weak smoke signal, not a success metric.
- Fix or remove flaky tests immediately.
- Use property-based testing sparingly, only when invariants are clear and the additional machinery is justified.
- Do not assert ordering unless ordering is part of the contract.

## Testability Is a Design Signal

Code that is consistently hard to test usually has unclear boundaries, mixed responsibilities, or too much hidden state. Use testing difficulty as a signal to simplify the design.

## Quick Adherence Check

Before finishing test work, verify:

- Does the test protect public behavior rather than internals?
- Would it survive a refactor that preserves the contract?
- Is the scenario name clear and behavior-focused?
- Is the setup visible, local, and deterministic?
- Are the assertions validating outputs, observable state, or genuine contract-level interactions?
- Are doubles being used only where they clarify the contract?
- Are integration tests limited to real boundaries?

## Summary

- Test contracts, not implementation details.
- Keep tests deterministic, focused, and easy to read.
- Prefer output and state assertions over interaction-heavy tests.
- Keep setup visible and use doubles deliberately.
- Use integration tests sparingly at boundaries.
- Measure success by confidence in behavior, not by test volume or coverage numbers.
