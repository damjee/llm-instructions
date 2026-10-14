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

Group tests by public operation. Keep each operation's tests contiguous and order them as follows:

1. **Successful happy paths:** Verify each distinct successful behavior.
2. **Failure paths:** Verify specified unsuccessful outcomes that do not constitute errors.
3. **Boundary paths:** Verify input limits, invalid inputs, and expected errors. Assert error types, variants, and meaningful structured fields rather than message text, unless wording is part of the public contract.

Multiple happy paths are appropriate when they specify distinct behaviors. Omit inapplicable categories rather than inventing tests.

Expected layout (illustrative, not a required naming syntax):

```text
operation A
  successful behavior A
  successful behavior B
  failure behavior A
  boundary behavior A
  boundary behavior B

operation B
  successful behavior A
  failure behavior A
  boundary behavior A
```

## Test Names

- Name the public operation and the distinct intended behavior concisely.
- Include only enough detail to distinguish one contract from another.
- Put concrete argument values, full expected outputs, and fixture details in test bodies rather than forcing them into names.
- Reject names too vague to identify a behavior, but do not require complete input/output specifications in names.

## Review Procedure

### 1. Establish the intended specification

Identify the module's requirements, intended public operations, and expected behaviors without using existing implementation to define the contract. Surface missing requirements before proceeding, do not infer.

### 2. Extract the names-only view

Extract test function names in their **original source order**, preserving enclosing test groups where relevant. Do not sort them. Pass only the names and grouping information, not test bodies or implementation code.

### 3. Independently validate layout and specifications

When the harness supports a fresh-context subagent, provide it **only**:

- The intended module requirements and public API specification.
- The ordered test names and their grouping.
- The layout and behavioral specification criteria from this skill.

If independent delegation or context isolation is unavailable, perform a names-only review and disclose that it was not an independent subagent review.

The reviewer must validate:

**Module layout**
- Tests are grouped by public operation, with each operation's tests contiguous.
- Within every operation, successful paths precede failure paths, which precede boundary paths (including errors).
- Tests are not misplaced, interleaved across operations, or otherwise out of order.

**Specification coverage**
- Every intended public operation has a recognizable test group.
- Distinct intended successful behaviors, failure conditions, and boundaries are represented.
- Names communicate distinguishable behavioral contracts without needing exhaustive input/output details.
- Missing, redundant, ambiguous, or unrelated specifications are identified.

This pass assesses what the **names claim to specify**. It cannot prove concrete argument coverage, expected values, or assertion correctness.

Report layout violations and specification gaps before examining test bodies. Treat this as a review gate: do not claim the specification passes while unresolved violations remain.

### 4. Validate test bodies

After the names-only review passes, inspect the test bodies against the intended requirements and named behaviors:

- Verify that each test actually exercises its named public operation and observable contract.
- Check concrete inputs and independently defined expected outputs or effects.
- Vary meaningful arguments and test their expected differences or intended invariance; include relevant argument interactions.
- Check failure and boundary conditions, including error types, variants, and structured fields.
- Identify weak assertions, tests of implementation details, and unnecessary duplication.
- Check whether plausible defects (such as ignored arguments or incorrect boundaries) would cause a test to fail.

Distinguish gaps visible from names alone from defects demonstrated only after reading test bodies. Do not modify code during a review.
