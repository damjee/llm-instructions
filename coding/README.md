# Documentation Index

This directory contains agent-ready coding standards and testing philosophy documents.

## Documents

### [Clean Code Standards](./clean-code-standards.md)
**Language-agnostic clean code principles**

Core topics:
- SOLID principles (Single Responsibility, Open/Closed, etc.)
- Clean code patterns (KISS, YAGNI, DRY, TDA)
- Function design (< 20 lines, single purpose)
- Early returns pattern (eliminate nesting)
- Verb-based function naming
- Static typing requirements
- Self-documenting code philosophy
- Refactoring workflow

### [Godot & GDScript Standards](./godot-gdscript-standards.md)
**Godot Engine and GDScript-specific conventions**

Core topics:
- GDScript official style guide (tabs, line length, operators)
- Naming conventions (files, classes, functions, signals, enums)
- Code order requirements (annotations → signals → enums → constants → exports)
- @export + assert() pattern for node wiring
- Godot-specific anti-patterns
- Static typing in GDScript
- Complete working examples

### [Testing Standards](./testing-standards.md)
**Contract-driven testing doctrine**

Core topics:
- Test the contract, not the implementation
- Intent over implementation
- Test structure philosophy (naming, assertions)
- Determinism requirements
- Mocking vs dependency injection policy
- Integration testing strategy
- Coverage policy (confidence over metrics)
- Zero tolerance for flaky tests
- Contract-focused examples

## Usage

These documents are formatted as agent instructions and can be used:
- As reference material for AI agents performing code reviews or refactoring
- As team guidelines for human developers
- As input to custom BMAD agents or workflows
- As prompts for code generation tasks

### How to Apply These Instructions

When imported into a project, include the relevant document(s) as system-level context. Apply Clean Code Standards as a baseline, then layer on domain-specific docs (e.g., Godot & GDScript Standards) as needed. Testing Standards apply alongside both.

**Conflict resolution:** Domain-specific documents (Godot & GDScript Standards) override general documents (Clean Code Standards) where they conflict.

## Document Relationships

```
Clean Code Standards (general)
    ↓
    Referenced by: Godot & GDScript Standards
    Referenced by: Testing Standards

Godot & GDScript Standards (Godot-specific)
    ↓
    Supplements: Clean Code Standards
    
Testing Standards (testing philosophy)
    ↓
    Companion to: Both Clean Code and Godot standards
```

All documents cross-reference each other where appropriate.
