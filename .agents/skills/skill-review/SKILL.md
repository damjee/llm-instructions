---
name: skill-review
description: Review agent skills for concise wording, precise instructions, and executable workflows. Use when asked to review, audit, or tighten a skill or SKILL.md.
---

# Skill Review

## Procedure

1. Locate the target skill; request its path or content if missing. Read it and references needed to understand its workflows. Identify triggers, outcomes, required behavior, and authorization boundaries.
2. Apply every checklist item below. Ground each finding in a specific passage and its effect on execution.
3. Propose the smallest correction that preserves intent, safety constraints, required formats, and exact tool or domain semantics. Resolve substantive ambiguity with the user before changing behavior.
4. Walk through each applicable workflow using the proposed wording: verify inputs, branch selection, actions, and completion criteria. Label walkthroughs separately from executed tests.
5. Return the review. Apply edits only when requested; after editing, repeat the checklist and run available skill validation.

## Review Checklist

- [ ] **Discovery:** The name and description identify the task and distinct invocation cases without attracting unrelated work.
- [ ] **Procedure:** Each applicable workflow specifies ordered actions, decision conditions, necessary inputs, and an observable completion criterion. Missing inputs and foreseeable failures have a clear next step where needed.
- [ ] **Precision:** Choose high-impact words that determine behavior: concrete verbs, established domain terms, and explicit conditions. Replace vague intensifiers or invented jargon with observable actions; retain qualifications that affect correctness.
- [ ] **Concision:** Each instruction changes a decision or action. Remove repetition, filler, stale guidance, and advice that adds no task-specific value. Judge brevity by preserved meaning, not a word quota.
- [ ] **Positive framing:** State the desired action first. Keep explicit prohibitions when required for safety or exact boundaries; pair them with the permitted action where useful.
- [ ] **Structure:** Use concise bullets or checklists for rules and numbered steps for procedures. Reserve prose for deliberately chosen philosophy, principles, or overview sections that improve judgment; omit them when they add no value.
- [ ] **Self-containment:** Keep essential instructions directly in the skill. Retain references only when conditional detail or reusable resources justify them, with clear loading conditions and valid targets.
- [ ] **Fidelity:** Preserve scope, approvals, exceptions, required output contracts, and meaningful distinctions. Keep sound wording unchanged when a rewrite offers no concrete benefit.

## Review Output

- Verdict: ready, needs revision, or blocked by missing context.
- Findings, ordered by execution impact: location → problem → consequence → precise replacement or deletion.
- Validation: workflows checked, tests actually run, and remaining uncertainty.
- If ready: say no changes are needed; omit empty findings and cosmetic rewrites.
