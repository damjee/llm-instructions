# Feedback: coding/testing-standards.md

## What Works Well

- **Strong philosophical framing** — "If a change preserves the public contract, tests should continue to pass" is a clear, memorable golden rule. This kind of single-sentence anchor helps LLMs prioritize correctly.
- **Explicit "do" and "don't" lists** — The Acceptable/Unacceptable Test Targets section with ✅/❌ is perfectly structured for LLM consumption. No ambiguity.
- **Mock vs. Fake distinction with examples** — This is one of the best parts of the doc. Many LLMs default to mocking everything. The side-by-side comparison of Mock vs. FakePaymentGateway is exactly the kind of example that changes LLM behavior.
- **"What This Testing Doctrine Rejects" section** — Priority-ordered rejection list is excellent. It tells the LLM what to actively avoid, which is often more useful than what to do.
- **Contract vs. implementation examples** — The damage calculator examples threading through the doc create a consistent mental model. Reusing the same domain (combat system) across examples is smart — it reduces cognitive load.

## Suggestions for Improvement

### 1. ~~No GDScript/Godot Testing Examples~~ [CORRECTION: This was incorrect feedback]

**This feedback was wrong.** The testing-standards.md doc is intentionally language-agnostic and should remain that way. Python examples are appropriate here as a universal illustration language. Language-specific testing patterns belong in separate, domain-specific docs if needed.

### 2. The DRY in Tests Section Subtly Discourages Parametrize — Clarify

The DRY section marks `@pytest.mark.parametrize` as "⚠️ RISKY" with the comment "Less clear what broke." This is a reasonable preference, but parametrize is also a very common and useful pattern for boundary/edge-case testing. Consider adding nuance:
- Parametrize is fine for **data-driven boundary tests** (e.g., testing 20 edge cases of a parser).
- Parametrize is risky for **behavioral tests** where each case tests a different invariant.

Without this nuance, an LLM might avoid parametrize entirely, leading to extremely verbose test files.

### 3. Missing: Test Organization / File Structure Guidance

The doc covers *how* to write individual tests but says nothing about:
- How to organize test files (mirror source structure? flat? by feature?)
- Naming conventions for test files
- How to group related tests (classes? modules? just flat functions?)
- Setup/teardown guidance

LLMs need this when asked to "write tests for this module" — they need to know where to put them and how to structure the file.

### 4. Missing: Error/Exception Testing Patterns

The "Acceptable Test Targets" section lists "Testing error conditions" but there are no examples of how to test for expected errors/exceptions. A pattern like:
```python
def test_calculate_damage_raises_when_units_are_none():
    with pytest.raises(ValueError):
        calculate_damage(None, None)
```
...would be valuable, especially since LLMs often struggle with error-path testing.

### 5. The Property-Based Testing Rejection Could Be Softer

"Avoided" is pretty absolute. The reasoning ("additional machinery and abstraction are not preferred") is valid for most cases, but there are domains (serialization, encoding, math-heavy code) where property-based testing is genuinely the right tool. Consider changing to:
> "Not the default approach. Consider only when explicit examples cannot reasonably cover the invariant space (e.g., serialization roundtrip tests)."

### 6. Integration Testing Section Is Light

The integration testing guidance is sound but brief. It would benefit from:
- A concrete example of a **bad** integration test (e.g., one that tests business logic through the HTTP layer instead of unit testing it directly).
- Guidance on test database management (in-memory DBs, transactions, cleanup).
- How many integration tests are "few" — a rough ratio like "aim for 80% unit / 20% integration" would give the LLM a concrete target.

### 7. Consider Adding a "Test Smell" Checklist

Similar to the refactoring workflow in Clean Code Standards, a checklist the LLM can run through when evaluating tests would be very actionable:
- [ ] Does this test break if I refactor the internals?
- [ ] Does this test name tell me what broke?
- [ ] Is this test deterministic?
- [ ] Am I testing the contract or the implementation?
- [ ] Am I using mocks when fakes would be better?

## Overall Rating

**Excellent testing philosophy doc.** The contract-driven approach is well-articulated and the examples are strong. The main gaps are: (1) missing test organization guidance, and (2) missing error-testing patterns. These are additive improvements — the existing content is solid and well-structured for LLM consumption. The language-agnostic nature of the doc is appropriate and should be preserved.
