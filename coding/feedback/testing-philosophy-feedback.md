# Feedback on testing-philosophy.md

## Overlap: Determinism First vs Testability as a Design Signal

**Issue**: This paragraph from "Determinism First" overlaps with the "Testability as a Design Signal" section:

> "Testable code favors functional principles: same input → same output, side effects minimized, errors returned explicitly. Difficulty testing often reveals design issues — testability is a design signal, not just a testing concern."

**Testability as a Design Signal** section already covers:
- If a module is difficult to test, that's an architectural signal
- Testing should follow architecture, but persistent testing difficulty suggests the module needs clearer boundaries

### Recommendation:

Move this paragraph into the "Testability as a Design Signal" section. The ideas about:
- Difficulty testing revealing design issues
- Testability being a design signal, not just a testing concern
- Favoring functional principles as a solution

...all belong together under the design signal concept, not split between two sections.

---

## Document Real Estate: Edge Case Sections Taking Too Much Space

**Issue**: These sections cover edge cases but occupy significant document real estate:

- Snapshot / Golden Test Policy
- Coverage Policy  
- Flaky Tests
- Property-Based Testing
- Data Structures and Contract Alignment

**Concern**: The amount of space these sections take will over-influence the model, giving these edge cases disproportionate weight compared to core principles.

### Recommendation:

Condense these into a single section (e.g., "Testing Practices & Edge Cases" or "Additional Testing Guidance") to:
- Reduce their prominence in the document
- Keep the focus on core principles
- Still provide guidance for these situations without over-emphasizing them
