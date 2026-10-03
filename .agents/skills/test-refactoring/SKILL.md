---
name: test-refactoring
description: Apply the user's test layout, naming, and AAA conventions when writing or refactoring tests, including clean-code or AAA requests on test files.
---

# Test Refactoring

## Philosophy

Tests specify observable behavior through public interfaces and real code paths. They remain valid when implementation changes without changing behavior.

Apply [Clean Code](../clean-code/SKILL.md) and the accessory for the language in scope. The test-specific rules below specialize its production-code layout and comment guidance.

## Layout and Naming

- Order files as Variables and Types → Tests → Local Helpers.
- Name tests for the specific behavior tested; make failure reasons explicit. Put the happy path first.
- Give distinct invalid cases separate, named tests with visible inputs and expected outcomes.
- Name variables for their intended role. Prefer `sut` for the system under test when local conventions leave the choice open.
- Keep setup and assertions visible in each test; avoid opaque helpers or loops that hide case-specific behavior.
- Separate Arrange, Act, and Assert with blank lines and one-word section comments such as `// Arrange`, using the language's comment syntax. Keep each section visually contiguous.

### Arrange

- Perform data setup, test-double initialization, and SUT initialization here.
- Initialize `sut` at the top when dependencies permit.
- Keep arrangement explicit; obtain user authorization before hiding it behind a helper.

### Act

- Exercise exactly one logical action of the SUT.
- Prefer one method call, storing its result here.

### Assert

- Contain the assertions only; keep setup in Arrange and SUT execution in Act.

## Test Doubles and Helpers

- Prefer Dummy → Stub → Fake → Spy → Mock, selecting the simplest sufficient double.
- Reserve spies and mocks for system boundaries: external APIs, time/randomness, and sometimes the filesystem or databases; prefer a test database when practical.
- Exercise owned classes, modules, and internal collaborators directly rather than spying or mocking them.
- Verify outcomes through the public interface rather than private methods, internal call counts/order, or direct database queries that bypass the interface.
- Keep helpers minimal and logic-free. Extract a local helper only when useful in at least 3 tests, or a shared helper in at least 3 test files. Preserve the explicit-arrangement approval requirement above.

## Review Checklist

Review in this priority order:

1. [ ] Nesting exceeds 3 indents.
2. [ ] A comment other than an AAA section marker is present.
3. [ ] Global variables or helpers obscure individual test setup.
4. [ ] Test execution code sits outside AAA sections.
5. [ ] Conditional logic is present in a test or helper.
6. [ ] A higher-order test double is used when a simpler one suffices.
7. [ ] AAA sections are missing, intermixed, or visually indistinct.
8. [ ] Act contains multiple logical actions.
9. [ ] Test name obscures the specific behavior tested.
10. [ ] Variable name obscures its intended role.

## Procedure

1. For new tests, apply the conventions above and run the relevant tests. Report their results and any verification blocked or unavailable. If only adding new tests, finish here; use steps 2–6 when refactoring existing tests.
2. For existing passing tests, verify the user has approved refactoring them before editing; ask if approval is missing.
3. Establish a passing test-suite baseline before refactoring. If tests fail or cannot run, report the blocker and establish a working baseline before proceeding.
4. Resolve only the highest-priority smell, then rerun the entire test suite. If a change causes failures, fix or revert it before continuing.
5. Restart the checklist from the top after each passing refactor.
6. Once no actionable smells remain, rerun the entire suite. Resolve regressions and restart the checklist; finish with the test results and any remaining blockers.
