# Godot / GDScript

Apply this on top of [SKILL.md](../SKILL.md).

## Defaults

- Use static typing.
- Prefer editor connections or explicit `connect()` calls in `_ready()`.
- Use `@export` to wire nodes in the editor.
- Keep node wiring explicit and easy to validate.
- Validate exported node references with `assert()` in `_ready()`.

```gdscript
@export var health_bar: ProgressBar

func _ready() -> void:
	assert(health_bar != null, "health_bar must be set in editor")
```

## Formatting

- Use tabs, not spaces.
- Keep lines under 100 characters. Prefer under 80 when practical.
- Use two blank lines between functions or classes, one inside functions for logical separation.
- Use one statement per line.
- Use one space around operators and after commas.
- Use trailing commas in multi-line arrays, dictionaries, and enums.
- Prefer `and`, `or`, and `not`, over `&&`, `||`, or `!`.
- Prefer double quotes unless single quotes reduce escaping.
- Include leading and trailing zeros: `0.5`, not `.5`; `10.0`, not `10.`

## Naming

- `PascalCase` for classes
- `CONSTANT_CASE` for constants and enums
- `snake_case` for files, functions, variables, and signals
- `_` prefix for private functions and variables
- Signal names use past tense.
- Function names should be verb-based and describe the action they perform.
- Boolean-returning functions should use predicate forms like `is_`, `has_`, and `can_`.

## Avoid

- Avoid string-based node access:

```gdscript
# BAD - fragile to scene reorganization
func _ready() -> void:
	get_node("UI/HealthBar").value = health
```

- Avoid deep node hierarchies in code:

```gdscript
# BAD - fragile runtime dependency on scene tree organization
var weapon = $Player/Body/RightHand/WeaponSlot/Weapon

# GOOD - direct reference
@export var weapon: Weapon

func _ready() -> void:
	assert(weapon != null, "weapon must be set in editor")
```

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
