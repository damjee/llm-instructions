# Feedback: coding/clean-code-standards.md

## What Works Well

- **Clear role assignment** — "You are an expert code refactoring agent" is textbook LLM prompting. Defining persona up front is one of the highest-impact techniques.
- **Explicit boundaries (What You Refactor vs. What You DON'T Change)** — This is excellent. LLMs drift without explicit constraints, and these sections act as guardrails. One of the strongest parts of the doc.
- **Rich examples with ✅/❌ patterns** — Few-shot examples are the single most effective way to steer LLM output. Every section has them, and they're well-chosen.
- **Step-by-step refactoring workflow** — Chain-of-thought/stepwise instructions dramatically improve LLM reasoning quality. The 5-step workflow is great.
- **CRITICAL markers** — Using "CRITICAL" to flag the most important rules helps the LLM weight instructions properly.

## Suggestions for Improvement

### 1. The Doc Is Language-Agnostic but All Examples Are Python

The purpose says "Language-agnostic" but every code example is Python. This could cause an LLM to over-index on Python idioms when working in other languages. Consider either:
- Adding a note: "Examples use Python for illustration; apply equivalent patterns in your target language."
- Or including 1-2 examples in a second language (e.g., GDScript, since that's your other doc).

### 2. Add Explicit Scoping for When This Doc Applies

The doc jumps straight into "You are an expert..." without telling the LLM *when* to use these rules. If this gets imported alongside other instructions, the LLM needs to know: "Apply these standards to all code refactoring and review tasks, regardless of language."

### 3. The "Core Mission" and "Summary" Are Somewhat Redundant

Both sections restate the same principles. LLMs handle repetition fine (it actually reinforces), but you could tighten the Summary to be a quick-scan checklist rather than re-explaining concepts already covered in detail above. The current summary is good but could be even more scannable.

### 4. Consider Adding a "When NOT to Refactor" Section

The doc is great at saying *what* to refactor, but doesn't address when to leave code alone. For example:
- Performance-critical hot paths where readability trades off with speed
- Code that's about to be deleted/replaced
- Generated code or third-party code

This would prevent an overzealous LLM from refactoring things it shouldn't touch.

### 5. The DRY Rule Could Use a Concrete Threshold Example

You mention "Abstract duplication after 3rd occurrence (Rule of Three)" which is great, but a before/after example showing the 1st, 2nd, and 3rd occurrence + the extraction would make this much more actionable for an LLM.

### 6. Magic Number Example Could Be Stronger

The magic number example (`if health > 50`) is good but simple. A real-world case with multiple magic numbers in one function would better demonstrate the pattern and give the LLM a richer example to generalize from.

### 7. Consider Adding Severity/Priority to Anti-Patterns

Not all anti-patterns are equally bad. If you ranked them (e.g., "Fix deep nesting FIRST, then long functions, then magic numbers, then poor names"), the LLM would make better triage decisions when refactoring a file with multiple issues.

## Overall Rating

**Very strong instruction document.** It follows nearly all best practices for LLM instruction design: clear role, explicit constraints, rich examples, step-by-step workflow, and strong guardrails. The main improvements are around edge-case handling (when NOT to refactor) and making the language-agnostic claim match the examples. This is one of the better LLM instruction docs I've seen.
