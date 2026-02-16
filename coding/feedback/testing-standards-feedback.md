# Feedback: Testing Standards

**Document:** testing-standards.md  
**Date:** 2026-02-15  
**Feedback Type:** Critique of existing content

---

## High-Level Issues

### 1. Document Naming Issue

**Problem:**
The document is named `testing-standards.md` but the content is more accurately described as a testing philosophy rather than concrete standards.

**Impact:**
The name doesn't accurately reflect the philosophical and doctrinal nature of the content, which focuses on principles and approach rather than specific standards.

**Recommendation:**
Rename document to `testing-philosophy.md` to better reflect its purpose and content.

---

### 2. Agent-Specific Language

**Problem:**
The document reads as instructions to an agent rather than general coding guidelines. For example:
- "You are an expert contract-focused testing agent."
- "Your role is to generate and evaluate tests..."

**Impact:**
Limits applicability as universal testing philosophy; the tone and audience are too narrow, reducing its usefulness as general guidance.

**Recommendation:**
Rewrite to be general guidelines applicable to any developer or context, not agent-specific instructions. Similar to how clean-code-standards.md was refactored to be universally applicable.

---

### 3. Missing References to Established Principles

**Problem:**
The document presents testing ideas without grounding them in established principles or authorities in the testing community.

**Comparison:**
The `clean-code-standards.md` document effectively references SOLID, DRY, and YAGNI, which adds credibility and context.

**Impact:**
Ideas appear novel when they actually align with established philosophies from recognized authorities like Bob Martin (Uncle Bob), Kent Beck, and Martin Fowler.

**Recommendation:**
Reference established testing principles, patterns, and authorities to ground the philosophy in recognized best practices. This adds credibility and helps readers connect concepts to broader industry knowledge.

---

### 4. Negative Instruction Heavy

**Problem:**
The document spends significant text explaining "what not to do" rather than "what to do." The content is prohibition-heavy with extensive anti-pattern lists.

**Impact:**
LLMs tend to perform poorly with negative/prohibition-heavy instructions. This approach is less effective for guiding positive behavior and teaching best practices.

**Recommendation:**
Reframe content to emphasize positive guidance and best practices rather than anti-patterns and prohibitions. Lead with what to do, and use anti-patterns sparingly for contrast when necessary.

---

## Section-Specific Issues

### 1. Determinism First: Code Design vs Testing Philosophy Overlap

**Section Affected:**
"Determinism First"

**Current Content:**
- "Core modules should prefer functional principles:"
- "Same input → same output"
- "Side effects minimized"
- "Errors returned explicitly"
- "Tests should focus on return values and explicit errors whenever possible."

**Problem:**
This content overlaps with code design principles rather than testing philosophy. The guidance about preferring functional principles, minimizing side effects, and returning errors explicitly belongs in code design documentation (like clean-code-standards.md), not testing philosophy.

**Impact:**
Blurs the boundary between testing philosophy and code design guidance. Creates potential duplication and overlap between documents.

**Note:**
The fix is unclear because the content legitimately spans both testing and code design concerns. The relationship between code design choices and testability is real and worth addressing.

---

### 2. Complexity and Architectural Signals: Architecture vs Testing Philosophy Overlap

**Section Affected:**
"Complexity and Architectural Signals"

**Current Content:**
- "If a module becomes: Branch-heavy, Edge-case heavy, Math-heavy, Difficult to reason about"
- "The solution is architectural clarity, not internal testing."
- "Refactor into smaller modules with clearer public contracts."
- "There is no universal rule for where to split — this is engineering judgment."
- "Testing should follow architecture, not dictate it."

**Problem:**
This content is about architecture and code design, not testing philosophy. The guidance on when to refactor, how to split modules, and architectural judgment is architectural guidance that happens to reference testing.

**Impact:**
Further blurs document boundaries. This guidance is architectural rather than test-focused, suggesting these concepts belong in a different document.

**Note:**
User suggests the need for a separate architecture document (e.g., `architecture-philosophy.md`) where these ideas could be properly developed and linked between documents.

---

### 3. DRY in Tests: Redundant with Clean Code Standards

**Section Affected:**
"DRY in Tests"

**Current Content:**
- "Repetition is acceptable."
- "Do not abstract tests prematurely."
- "Only refactor duplicated test logic after it meaningfully repeats (Rule of Three applies)."
- "Clarity over cleverness."

**Problem:**
DRY is already covered in `clean-code-standards.md`. Tests are code and should adhere to clean code principles by default. This section creates double-documentation of the same principle.

**Impact:**
Double-documenting principles creates maintenance burden and potential for inconsistency. If DRY guidance changes in one place, it needs to change in both.

**Recommendation:**
Remove this section. Tests should follow clean code standards including DRY guidance without needing separate test-specific DRY documentation.

---

## Emerging Patterns

**Multiple Sections Overlap with Architecture/Design:**
The "Determinism First" and "Complexity and Architectural Signals" sections both indicate content that spans testing, design, and architecture. This pattern suggests:

1. A need for clearer boundaries between testing philosophy and code/architecture design
2. Potentially a need for a separate `architecture-philosophy.md` or similar document
3. Content could link between documents rather than duplicate concerns across multiple files

**Recommendation for Future Work:**
Consider creating an architecture-focused document to handle the intersection of testing, design, and architectural decisions, allowing each document to focus on its core domain while referencing related concepts.
