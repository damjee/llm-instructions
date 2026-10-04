---
name: clean-code-gdscript
description: Apply Godot/GDScript clean-code conventions when refactoring, fixing, or reviewing GDScript, including node wiring and Godot file order.
---

# Clean Code: Godot / GDScript

## Types, Nodes, and Signals

- Use static typing.
- **Wire node references in the editor** with `@export`; validate them with `assert()` in `_ready()`.
- Avoid string-based node access and deep node-path traversal, such as `get_node("Player/WeaponSlot/Weapon")` or `$Player/WeaponSlot/Weapon`.
- Prefer editor signal connections or explicit `connect()` calls in `_ready()`.

```gdscript
@export var health_bar: ProgressBar

func _ready() -> void:
	assert(health_bar != null, "health_bar must be set in editor")
```

## Formatting

- Indent with tabs; use one statement per line.
- Keep lines under 100 characters, preferably under 80 when practical.
- Use two blank lines between functions or classes, one inside functions for logical separation.
- Use one space around operators and after commas.
- Use trailing commas in multiline arrays, dictionaries, and enums.
- Prefer `and`, `or`, and `not` over `&&`, `||`, and `!`.
- Prefer double quotes unless single quotes reduce escaping.
- Include leading and trailing zeros: `0.5`, not `.5`; `10.0`, not `10.`

## Naming

- Classes and enum names: `PascalCase`
- Constants and enum members: `CONSTANT_CASE`
- Files, functions, variables, and signals: `snake_case`
- Private functions and variables: `_` prefix
- Signals: past tense
- Boolean-returning functions should use predicates such as `is_`, `has_`, and `can_`.

## Godot File Order

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
