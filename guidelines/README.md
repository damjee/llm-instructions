# Guidelines Router

If you are an LLM or automation agent, start here. Do not load every guide by default. Load only the minimum set of files needed for the current task.

## Default Load Order

1. Load [`clean-code-standards.md`](./clean-code-standards.md) for any code implementation, refactor, review, or bug fix.
2. Load [`testing-philosophy.md`](./testing-philosophy.md) only when tests, test failures, or testability are part of the task.
3. Load a language-specific guide under [`languages/`](./languages/) only when the repository or task matches that language or framework.

## Minimal Load Recipes

| Scenario | Files to load |
| --- | --- |
| Generic implementation, refactor, or review | `clean-code-standards.md` |
| Test creation, test fixes, or test strategy | `clean-code-standards.md`, `testing-philosophy.md` |
| Python implementation or review | `clean-code-standards.md`, `languages/python/python-standards.md` |
| Python work with tests | `clean-code-standards.md`, `testing-philosophy.md`, `languages/python/python-standards.md` |
| JavaScript implementation or review | `clean-code-standards.md`, `languages/javascript/javascript-standards.md` |
| JavaScript work with tests | `clean-code-standards.md`, `testing-philosophy.md`, `languages/javascript/javascript-standards.md` |
| TypeScript implementation or review | `clean-code-standards.md`, `languages/typescript/typescript-standards.md` |
| TypeScript work with tests | `clean-code-standards.md`, `testing-philosophy.md`, `languages/typescript/typescript-standards.md` |
| Godot/GDScript implementation or review | `clean-code-standards.md`, `languages/godot/gdscript-standards.md` |
| Godot/GDScript work with tests | `clean-code-standards.md`, `testing-philosophy.md`, `languages/godot/gdscript-standards.md` |
| Repository routing only | This file, then `manifest.json` if structured metadata is needed |

## Selection Rules

- Load `clean-code-standards.md` by default for any source-code task.
- Add `testing-philosophy.md` only when tests or testability are in scope.
- Add a language-specific guide only when the task matches that language or framework.
- Skip unrelated language guides.
- Skip legacy `coding/` content; it is ignored and not part of the current guidance set.

## Stop When You Have Enough Context

Once you have loaded the guides required by the current task, stop. Do not load additional guides unless the task scope expands.

## Guide Catalog

### [`clean-code-standards.md`](./clean-code-standards.md)
Baseline guidance for code structure, naming, clarity, and refactoring.

### [`testing-philosophy.md`](./testing-philosophy.md)
Behavior-focused testing guidance for refactor-safe tests and test design.

### [`languages/python/python-standards.md`](./languages/python/python-standards.md)
Python conventions that refine the general guidance for Python repositories and tasks.

### [`languages/javascript/javascript-standards.md`](./languages/javascript/javascript-standards.md)
JavaScript conventions that refine the general guidance for browser and Node.js code.

### [`languages/typescript/typescript-standards.md`](./languages/typescript/typescript-standards.md)
TypeScript conventions that refine the general guidance for typed JavaScript codebases.

### [`languages/godot/gdscript-standards.md`](./languages/godot/gdscript-standards.md)
Godot and GDScript conventions that refine the general guidance for that stack.

## Machine-Readable Index

Use [`manifest.json`](./manifest.json) when an agent prefers structured metadata over prose.
