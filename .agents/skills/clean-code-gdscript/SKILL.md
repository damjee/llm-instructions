---
name: clean-code-gdscript
description: Apply Godot/GDScript clean-code conventions when refactoring, fixing, or reviewing GDScript, including node wiring and Godot file order. Use with the clean-code core.
---

# Clean Code: Godot / GDScript

Apply [Clean Code](../clean-code/SKILL.md) with the conventions below. Use its procedure and completion checks. The Godot file order below specializes the core's general layout.

## Defaults

- Use static typing.
- Prefer editor connections or explicit `connect()` calls in `_ready()`.
- Validate exported node references with `assert()` in `_ready()`.

```gdscript
@export var health_bar: ProgressBar

func _ready() -> void:
	assert(health_bar != null, "health_bar must be set in editor")
```

## Formatting

- Indent with tabs.
- Keep lines under 100 characters. Prefer under 80 when practical.
- Use two blank lines between functions or classes, one inside functions for logical separation.
- Use one statement per line.
- Use one space around operators and after commas.
- Use trailing commas in multi-line arrays, dictionaries, and enums.
- Prefer `and`, `or`, and `not`, over `&&`, `||`, or `!`.
- Prefer double quotes unless single quotes reduce escaping.
- Include leading and trailing zeros: `0.5`, not `.5`; `10.0`, not `10.`

## Naming

- `PascalCase` for classes and enum names
- `CONSTANT_CASE` for constants and enum members
- `snake_case` for files, functions, variables, and signals
- `_` prefix for private functions and variables
- Signal names use past tense.
- Boolean-returning functions should use predicate forms like `is_`, `has_`, and `can_`.

## Node References

- Use `@export` to wire node references in the editor; keep node wiring explicit and easy to validate.
- Avoid string-based node access and deep node-path traversal, such as `get_node("Player/WeaponSlot/Weapon")` or `$Player/WeaponSlot/Weapon`.

## Code Order

Follow this order in GDScript files:

1. Annotations
2. Class declaration
3. Doc comment, only if needed for API docs
4. Signals
5. Enums
6. Constants
7. `@export` variables
8. Public variables
9. Private variables
10. `@onready` variables
11. Built-in virtual methods in Godot order
12. Public methods
13. Private methods

