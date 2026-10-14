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

## Procedure

To validate a test module conforms to behavior test design do the following:

1. Validate layout
2. Validate specifications
3. Validate tests detail support specification

### Validate layout
1. Create a hidden temporary artifact copy of the test module. Dispose of the artifact on skill completion.
2. Reduce the artifact to contain only function names
3. Give the subagent the module requirements and temp artifact only

The subagent must independently verify that the test conforms to layout requirements and correctly documents all module specifications.

5. Once subagent independent review passes review the test detail and verify each test implementation supports the intended behavioral requirement as defined by the test name