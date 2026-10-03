---
name: skill-review
description: Review agent skills for fidelity, high-impact wording, and executable workflows. Use when asked to review, audit, or tighten a skill or SKILL.md.
---

# Skill Review

## Procedure

1. Locate the target skill; request its path or content if missing. Read it and references needed to understand its workflows. Identify triggers, outcomes, required behavior, and authorization boundaries. When reviewing a rewrite, compare it with the original when available.
2. Apply every checklist item below. Ground each finding in a specific passage and its effect on execution.
3. Propose the smallest correction that preserves intent, safety constraints, required formats, and exact tool or domain semantics. Resolve substantive ambiguity with the user before changing behavior.
4. Walk through each applicable workflow using the proposed wording: verify inputs, branch selection, actions, and completion criteria. Label walkthroughs separately from executed tests.
5. Return the review. Apply edits only when requested; after editing, repeat the checklist and run available skill validation.

## Review Checklist

- [ ] **Discovery:** The name and description identify the task and distinct invocation cases without attracting unrelated work.
- [ ] **Procedure:** Each applicable workflow specifies ordered actions, decision conditions, necessary inputs, and an observable completion criterion. Missing inputs and foreseeable failures have a clear next step where needed.
- [ ] **Precision:** Look for literal paraphrases that flatten established concepts, contrasts, or emphasis. Recover their force with high-impact words that steer judgment without changing meaning. Judge wording by its behavioral effect, not how exhaustively it spells things out. Clarify ambiguity when it changes behavior; otherwise trust the model to interpret it.
- [ ] **Concision:** Each instruction changes a decision or action. Remove repetition, filler, stale guidance, and advice that adds no task-specific value. Judge brevity by preserved meaning, not a word quota.
- [ ] **Positive framing:** Prefer desired actions where they communicate better. Preserve useful negative checks, diagnostic signals, and contrasts.
- [ ] **Structure:** Use concise bullets or checklists for rules and numbered steps for procedures. Reserve prose for deliberately chosen philosophy, principles, or overview sections that improve judgment; omit them when they add no value.
- [ ] **Self-containment:** Keep essential instructions directly in the skill. Retain references only when conditional detail or reusable resources justify them, with clear loading conditions and valid targets.
- [ ] **Fidelity:** Preserve scope, priorities, approvals, exceptions, required output contracts, and meaningful distinctions. Keep the author’s voice and the granularity of independent checks. Compare meaning and behavioral effect with the original when available; keep sound wording unchanged unless a change offers a concrete benefit.

## Review Output

- Verdict: ready, needs revision, or blocked by missing context.
- Findings, ordered by execution impact: location → problem → consequence → precise replacement or deletion.
- Validation: workflows checked, tests actually run, and remaining uncertainty.
- If ready: say no changes are needed; omit empty findings and cosmetic rewrites.

