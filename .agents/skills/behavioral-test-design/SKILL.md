---
name: behavioral-test-design
description: Design and review tests as specifications of intended public API behavior. Use when defining behavioral coverage, evaluating API legibility, or checking happy paths, failures, boundaries, arguments, and assertions.
---

# Behavioral Test Design

## Philosophy

Tests, not existing implementation, define a module's intended behavior. Treat tests as executable specifications of the public API. Establish specifications from requirements, never infer them from existing code.

## Definition

A **public operation** is a single callable entry point exposed by a module's intended public API, such as a constructor, method, function, RPC endpoint, or equivalent interface operation.

Each exposed entry point is a distinct operation. Chaining or grouping calls does not combine them into a single operation.

## Layout

Specify each public operation independently through concrete inputs and expected outcomes. Test every distinct expected behavior, including how meaningful input variations affect the result.

Organize tests in this order:

1. **Successful happy paths:** Verify each distinct successful behavior.
2. **Failure paths:** Verify specified unsuccessful outcomes that do not constitute errors.
3. **Boundary paths:** Verify input limits, invalid inputs, and expected errors. Assert error types, variants, and meaningful structured fields rather than message text, unless wording is part of the public contract.

A test module should have a layout matching the form:

1.1.1. operation 1 behavior A
1.1.2. operation 1 behavior B
1.2.1. operarion 1 failure behavior C
1.3.1. operation 1 boundry behavior A
1.3.1. operation 1 boundry behavior B
2.1.1. operation 2 behvaior A
2.2.1. operation 3 failure behavior A
etc until all public operations are documented

## Behavioral Coverage

- Inventory intended public operations from requirements. Require direct behavioral coverage for each.
- Vary each meaningful argument while holding others constant where practical. Assert the expected difference or intended invariance.
- Test argument interactions when one argument changes another's valid range or behavior.
- Define expected outcomes independently of implementation results or duplicated implementation algorithms.
- Assert promised outputs, state changes, or observable effects; success alone is insufficient when a specific result is expected.
- Ensure plausible defects, such as ignored arguments, incorrect outcomes, and wrong boundaries, would fail at least one test.
- Test public contracts, not internal helpers, reference algorithms, or implementation details.
- Prefer readable representative cases over redundant tests or exhaustive matrices that obscure the specification.

## Assessment

1. Establish requirements and inventory intended public operations before consulting implementation.
2. Read tests to reconstruct the API and its intended behavior. Record ambiguities and unspecified contracts.
3. Map each operation to its happy, failure, and boundary cases; identify missing behavioral or argument coverage.
4. Map each test back to a public contract; identify irrelevant, redundant, or weak tests.
5. Inspect implementation to identify divergences from the intended contract, never to invent expected behavior.
6. Report findings by operation with evidence and coverage limits. Do not modify code during review.
