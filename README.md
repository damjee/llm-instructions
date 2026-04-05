# LLM Instructions

A collection of instruction documents for AI agents and language models.

## Structure

### [guidelines/](./guidelines/)
Core guidance documents for code quality and testing. If you are an AI agent, start with [`guidelines/README.md`](./guidelines/README.md) and load only the files it recommends.

- **[README.md](./guidelines/README.md)** - Task router with minimal load recipes and file selection rules
- **[manifest.json](./guidelines/manifest.json)** - Machine-readable guide index with load and skip metadata
- **[clean-code-standards.md](./guidelines/clean-code-standards.md)** - Language-agnostic clean code principles
- **[testing-philosophy.md](./guidelines/testing-philosophy.md)** - Contract-driven testing philosophy
- **[languages/python/python-standards.md](./guidelines/languages/python/python-standards.md)** - Python-specific conventions and starter preferences
- **[languages/javascript/javascript-standards.md](./guidelines/languages/javascript/javascript-standards.md)** - JavaScript-specific conventions and starter preferences
- **[languages/typescript/typescript-standards.md](./guidelines/languages/typescript/typescript-standards.md)** - TypeScript-specific conventions and starter preferences
- **[languages/godot/gdscript-standards.md](./guidelines/languages/godot/gdscript-standards.md)** - Godot/GDScript-specific conventions and preferences

## Usage

These documents are designed to be:
- **Agent instructions** - Feed directly to AI coding assistants
- **Reference material** - For human developers
- **Prompt templates** - For code generation tasks
- **Style guides** - For team consistency

For LLMs, load [`guidelines/README.md`](./guidelines/README.md) first, then follow its routing rules instead of loading every file in the repository.

## Philosophy

All documents follow these principles:
- Clear, actionable instructions
- Formatted for AI consumption
- Based on practical experience
- Focused on quality without breaking functionality
