# Feedback: coding/README.md

## What Works Well

- **Clear document relationships diagram** — The ASCII dependency graph at the bottom is excellent for LLM context; it tells the agent how docs relate without ambiguity.
- **Structured index with topic lists** — Each doc gets a bullet list of core topics, which is great for an LLM to quickly determine which doc is relevant.
- **Usage section** — Explicitly stating these are "agent instructions" sets the right frame for an LLM consumer.

## Suggestions for Improvement

### 1. Add a "How to Use These Instructions" Section for LLMs

Right now, the README describes what the docs *contain* but doesn't tell the LLM *how to apply them*. Consider adding a short preamble like:

> "When imported into a project, include the relevant document(s) as system-level context. Apply Clean Code Standards as a baseline, then layer on domain-specific docs (e.g., Godot & GDScript Standards) as needed."

This gives the LLM a clear **priority/layering model** — a best practice for multi-document instruction sets.

### 2. Specify Conflict Resolution Order

The document relationships show cross-references, but don't say what happens when docs conflict (e.g., if Clean Code says one thing about naming and GDScript says another). Add a single line like:

> "Domain-specific documents (Godot & GDScript) override general documents (Clean Code) where they conflict."

LLMs perform significantly better when they know which instruction takes priority.

### 3. Consider Adding a "Quick Reference" or TL;DR

For token-constrained contexts (smaller context windows or many files loaded), a 5-10 bullet "universal rules" summary that applies across all docs would be valuable. This gives the LLM the highest-signal instructions even if not all docs are loaded.

### 4. The "Document Relationships" Diagram Has a Minor Inconsistency

The diagram says Testing Standards "Applies to: Both Clean Code and Godot standards" — but testing standards don't really *apply to* coding standards; they're a *parallel concern*. Consider rephrasing to "Companion to" or "Used alongside" for clarity.

## Overall Rating

**Strong foundation.** The README does its job as an index well. The main gap is that it's written more for a human browsing a repo than for an LLM that needs to understand instruction priority and application order. A few targeted additions would make it significantly more effective as an LLM-facing entrypoint.
