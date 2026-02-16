# Godot & GDScript Standards

**Purpose:** Godot-specific coding conventions and GDScript style guide  
**Audience:** AI agents working with GDScript and Godot Engine projects  
**Scope:** Godot Engine and GDScript language-specific practices  
**Related Documents:** [Clean Code Standards](./clean-code-standards.md), [Testing Philosophy](./testing-philosophy.md)

---

You are an expert GDScript refactoring agent. This document defines Godot-specific standards that supplement the general clean code principles.

## Core Requirements

Apply these standards on top of [Clean Code Standards](./clean-code-standards.md). This document adds **Godot-specific** conventions and **user preferences** for this project.

---

## User Preferences for This Project

### Verb-Based Function Names

**Functions must clearly express ACTION with verb prefixes:**

```gdscript
# ✅ GOOD - Verb-based names
func calculate_damage() -> int:
func spawn_enemy_at(position: Vector2) -> void:
func check_collision_with_player() -> bool:
func update_health_bar_display() -> void:
func is_player_within_range() -> bool:
func has_required_item() -> bool:

# ❌ BAD - Noun-based or unclear names
func damage() -> int:  # Missing verb
func enemy(position: Vector2) -> void:  # Missing verb
func collision() -> bool:  # Missing verb
func health_bar() -> void:  # Missing verb
```

**Verb prefixes to use:**
- `calculate_` - for computations
- `update_` - for state changes
- `check_` / `is_` / `has_` / `can_` - for boolean queries
- `get_` / `set_` - for getters/setters
- `spawn_` / `create_` - for instantiation
- `apply_` - for applying effects
- `handle_` - for event handlers
- `process_` - for processing operations
- `validate_` - for validation

### Static Typing Required

**ALWAYS use explicit type annotations everywhere:**

```gdscript
# ✅ GOOD - Explicit types everywhere
var health: int = 100
var speed: float = 5.5
var player_name: String = "Hero"
var items: Array[Item] = []
var position: Vector2 = Vector2.ZERO

func calculate_total(items: Array[Item]) -> int:
	var total: int = 0
	for item in items:
		total += item.value
	return total

# ❌ BAD - Missing types
var health = 100
var speed = 5.5
var player_name = "Hero"
var items = []

func calculate_total(items):
	var total = 0
	for item in items:
		total += item.value
	return total
```

**Benefits of static typing:**
- Catches errors at parse time
- Improves editor autocomplete and refactoring
- Self-documents expected types
- Enables better static analysis

### No Types in Variable Names

**Variable names describe PURPOSE, not type (type is in annotation):**

```gdscript
# ✅ GOOD - Descriptive names, types in annotations
var player: Player
var enemy_count: int
var spawn_timer: Timer
var health_bar: ProgressBar

# ❌ BAD - Hungarian notation (redundant with types)
var player_node: Player
var int_enemy_count: int
var timer_spawn: Timer
var progress_bar_health: ProgressBar
```

---

## GDScript Official Style Guide

### Formatting

#### Indentation and Whitespace
- **Indentation:** Tabs only (not spaces)
- **Line length:** < 100 characters (prefer < 80)
- **Blank lines:** 2 between functions/classes, 1 inside functions for logical separation
- **One statement per line** (no `if x: return y` on same line)
- **Whitespace:** One space around operators, after commas

```gdscript
# ✅ GOOD - tab indentation, blank lines between functions
func calculate_damage(base: int, multiplier: float) -> int:
	var result: int = int(base * multiplier)
	return result


func apply_healing(amount: int) -> void:
	health += amount

# ❌ BAD - spaces instead of tabs, no blank lines between functions
func calculate_damage(base: int, multiplier: float) -> int:
    var result: int = int(base * multiplier)
    return result
func apply_healing(amount: int) -> void:
    health += amount
```

#### Multi-line Constructs
- **Trailing commas** in multi-line arrays/dicts/enums

```gdscript
# ✅ GOOD - Trailing comma
var items: Array[String] = [
"sword",
"shield",
"potion",
]

enum State {
IDLE,
RUNNING,
JUMPING,
}

# ❌ BAD - No trailing comma
var items: Array[String] = [
"sword",
"shield",
"potion"
]
```

#### Operators and Literals
- **Boolean operators:** `and`, `or`, `not` (not `&&`, `||`, `!`)
- **Quotes:** Prefer double quotes `"text"` (single quotes only to reduce escaping)
- **Numbers:** Include leading/trailing zeros: `0.5` not `.5`, `10.0` not `10.`

```gdscript
# ✅ GOOD
if is_alive and not is_stunned:
var damage: float = 10.0
var chance: float = 0.5

# ❌ BAD
if is_alive && !is_stunned:
var damage: float = 10.
var chance: float = .5
```

---

## Naming Conventions

### File Names
- **Files:** `snake_case` (e.g., `player_controller.gd`, `enemy_spawner.gd`)

### Class Names
- **Classes:** `PascalCase` (e.g., `class_name PlayerController`, `class_name EnemySpawner`)

```gdscript
# ✅ GOOD
class_name PlayerController
extends CharacterBody2D

# ❌ BAD
class_name player_controller
extends CharacterBody2D
```

### Functions
- **Functions:** `snake_case` with **verb prefix** (user preference - see above)
- **Private functions:** Underscore prefix (e.g., `func _calculate_internal():`)

```gdscript
# ✅ GOOD
func calculate_damage() -> int:
func spawn_enemy_at(position: Vector2) -> void:
func is_player_alive() -> bool:
func _initialize_internal_state() -> void:

# ❌ BAD
func Damage() -> int:
func createEnemy(position: Vector2) -> void:
func playerAlive() -> bool:
func damage() -> int:  # Missing verb prefix
```

### Variables
- **Variables:** `snake_case` (e.g., `var player_health: int`)
- **Constants:** `CONSTANT_CASE` (e.g., `const MAX_HEALTH: int = 100`)
- **Private variables:** Underscore prefix (e.g., `var _internal_counter: int`)

```gdscript
# ✅ GOOD
var current_health: int = 100
const MAX_SPEED: float = 400.0
var _velocity: Vector2 = Vector2.ZERO

# ❌ BAD
var CurrentHealth: int = 100
const maxSpeed: float = 400.0
var velocity: Vector2 = Vector2.ZERO  # Should be private
```

### Signals
- **Signals:** `snake_case`, past tense (e.g., `signal health_changed`, `signal player_died`)

```gdscript
# ✅ GOOD
signal health_changed(new_health: int)
signal player_died
signal item_collected(item: Item)

# ❌ BAD
signal healthChange(new_health: int)
signal PlayerDied
signal collectItem(item: Item)
```

### Enums
- **Enums:** `PascalCase` name, `CONSTANT_CASE` members

```gdscript
# ✅ GOOD
enum State {
IDLE,
RUNNING,
JUMPING,
}

enum DamageType {
PHYSICAL,
MAGICAL,
TRUE_DAMAGE,
}

# ❌ BAD
enum state {
idle,
running,
jumping,
}
```

---

## Code Order

Follow this exact order in all GDScript files:

```gdscript
# 1. Annotations
@tool
@icon("res://icon.png")

# 2. Class declaration
class_name MyClass
extends Node

# 3. Doc comment (optional, only if truly needed for API docs)
## Brief class description.

# 4. Signals
signal health_changed(new_health: int)
signal player_died

# 5. Enums
enum State {
IDLE,
RUNNING,
JUMPING,
}

# 6. Constants
const MAX_SPEED: int = 400
const JUMP_VELOCITY: int = -800

# 7. @export variables
@export var max_health: int = 100
@export var speed: float = 300.0

# 8. Public variables
var current_health: int = 100
var is_alive: bool = true

# 9. Private variables
var _velocity: Vector2 = Vector2.ZERO
var _state: State = State.IDLE

# 10. @onready variables
@onready var sprite: Sprite2D = $Sprite2D
@onready var collision: CollisionShape2D = $CollisionShape2D

# 11. Built-in virtual methods (in order: _init, _enter_tree, _ready, _process, _physics_process, _input, _unhandled_input, etc.)
func _ready() -> void:
assert(sprite != null, "sprite must be wired in editor")
assert(collision != null, "collision must be wired in editor")
_initialize_player()


func _process(delta: float) -> void:
_update_movement(delta)


func _physics_process(delta: float) -> void:
apply_physics(delta)


# 12. Public methods
func take_damage(amount: int) -> void:
if not is_alive:
return

current_health -= amount
health_changed.emit(current_health)

if current_health <= 0:
_die()


func heal(amount: int) -> void:
if not is_alive:
return

current_health = min(current_health + amount, max_health)
health_changed.emit(current_health)


# 13. Private methods
func _initialize_player() -> void:
current_health = max_health
is_alive = true


func _die() -> void:
is_alive = false
player_died.emit()
```

---

## Godot-Specific Patterns

### @export + assert() Pattern

**Use @export to wire nodes in editor, validate with assert():**

```gdscript
class_name Player
extends CharacterBody3D

# Export variables for editor wiring
@export var health_bar: ProgressBar
@export var weapon: Node3D
@export var animation_player: AnimationPlayer

func _ready() -> void:
# Validate all editor-wired nodes
assert(health_bar != null, "health_bar must be set in editor")
assert(weapon != null, "weapon must be set in editor")
assert(animation_player != null, "animation_player must be set in editor")

_initialize_player()
```

**Why this pattern:**
- Editor wiring is explicit and inspectable
- Runtime validation catches missing connections early
- No string-based node paths (`$NodePath`) scattered in code
- Clearer dependencies

### Node References

**Prefer @export over @onready for external nodes:**

```gdscript
# ✅ GOOD - Editor-wired, validated
@export var health_bar: ProgressBar

func _ready() -> void:
assert(health_bar != null, "health_bar must be set in editor")

# ⚠️ ACCEPTABLE - For child nodes in same scene
@onready var collision: CollisionShape2D = $CollisionShape2D

# ❌ BAD - String paths throughout code
func _ready() -> void:
get_node("HealthBar").value = 100
```

### Signal Connections

**Prefer editor connections or explicit connect() calls in _ready():**

```gdscript
# ✅ GOOD - Explicit connection with validation
@export var button: Button

func _ready() -> void:
assert(button != null, "button must be set in editor")
button.pressed.connect(_on_button_pressed)


func _on_button_pressed() -> void:
print("Button pressed")
```

### Static Typing in GDScript

**ALWAYS use explicit types (from Clean Code Standards):**

```gdscript
# ✅ GOOD - Full typing
var health: int = 100
var enemies: Array[Enemy] = []
var position: Vector2 = Vector2.ZERO

func calculate_total(items: Array[Item]) -> int:
var total: int = 0
for item in items:
total += item.value
return total

# ❌ BAD - Missing types
var health = 100
var enemies = []
var position = Vector2.ZERO

func calculate_total(items):
var total = 0
for item in items:
total += item.value
return total
```

---

## GDScript-Specific Anti-Patterns

### Avoid String-Based Node Access

```gdscript
# ❌ BAD - String-based access
func update_ui():
get_node("UI/HealthBar").value = health
get_node("UI/ScoreLabel").text = str(score)

# ✅ GOOD - Typed references
@export var health_bar: ProgressBar
@export var score_label: Label

func _ready() -> void:
assert(health_bar != null, "health_bar must be set in editor")
assert(score_label != null, "score_label must be set in editor")


func update_ui() -> void:
health_bar.value = health
score_label.text = str(score)
```

### Avoid Deep Node Hierarchies in Code

```gdscript
# ❌ BAD - Fragile path navigation
var weapon = $Player/Body/RightHand/WeaponSlot/Weapon

# ✅ GOOD - Direct reference
@export var weapon: Weapon

func _ready() -> void:
assert(weapon != null, "weapon must be set in editor")
```

### Avoid Implicit Type Conversions

```gdscript
# ❌ BAD - Implicit conversion
var damage = 10.5
target.health -= damage  # If health is int, this truncates silently

# ✅ GOOD - Explicit conversion
var damage: float = 10.5
target.health -= int(damage)
```

---

## Complete Example

```gdscript
@tool
@icon("res://icons/player.png")
class_name Player
extends CharacterBody3D

## Player controller with health, movement, and combat.

signal health_changed(new_health: int)
signal player_died
signal damage_taken(amount: int, attacker: Node)

enum State {
IDLE,
RUNNING,
JUMPING,
ATTACKING,
}

const MAX_SPEED: float = 400.0
const JUMP_VELOCITY: float = -800.0
const GRAVITY: float = 980.0

@export var max_health: int = 100
@export var acceleration: float = 2000.0

var current_health: int = 100
var is_alive: bool = true

var _velocity: Vector3 = Vector3.ZERO
var _state: State = State.IDLE

@onready var animation_player: AnimationPlayer = $AnimationPlayer
@onready var collision_shape: CollisionShape3D = $CollisionShape3D


func _ready() -> void:
assert(animation_player != null, "animation_player must be present")
assert(collision_shape != null, "collision_shape must be present")

_initialize_player()


func _physics_process(delta: float) -> void:
if not is_alive:
return

_update_movement(delta)
_update_animation()


func take_damage(amount: int, attacker: Node = null) -> void:
if not is_alive:
return

if amount <= 0:
return

current_health -= amount
health_changed.emit(current_health)
damage_taken.emit(amount, attacker)

if current_health <= 0:
_die()


func heal(amount: int) -> void:
if not is_alive:
return

if amount <= 0:
return

current_health = min(current_health + amount, max_health)
health_changed.emit(current_health)


func _initialize_player() -> void:
current_health = max_health
is_alive = true
_state = State.IDLE


func _die() -> void:
is_alive = false
_state = State.IDLE
player_died.emit()
animation_player.play("death")


func _update_movement(delta: float) -> void:
var input_direction: Vector2 = _get_input_direction()

if input_direction.length() > 0.0:
_state = State.RUNNING
else:
_state = State.IDLE

_apply_movement(input_direction, delta)


func _update_animation() -> void:
match _state:
State.IDLE:
animation_player.play("idle")
State.RUNNING:
animation_player.play("run")
State.JUMPING:
animation_player.play("jump")
State.ATTACKING:
animation_player.play("attack")


func _apply_movement(direction: Vector2, delta: float) -> void:
var target_velocity: Vector3 = Vector3(direction.x, 0.0, direction.y) * MAX_SPEED
_velocity = _velocity.lerp(target_velocity, acceleration * delta)
velocity = _velocity
move_and_slide()


func _get_input_direction() -> Vector2:
var input_x: float = Input.get_action_strength("move_right") - Input.get_action_strength("move_left")
var input_y: float = Input.get_action_strength("move_down") - Input.get_action_strength("move_up")
return Vector2(input_x, input_y).normalized()
```

---

## Summary

- **TABS NOT SPACES** - GDScript uses tabs for indentation
- **CODE ORDER** - Follow strict ordering: signals → enums → constants → exports → vars → @onready → methods
- **@export + assert()** - Wire nodes in editor, validate at runtime
- **`and`/`or`/`not`** - Use word operators, not symbols
- **VERB-BASED NAMES** - All functions must have action verb prefixes (user preference)
- **STATIC TYPING EVERYWHERE** - Full type annotations required (user preference)
- **NO TYPE IN NAMES** - Variable names describe purpose, not type
- **AVOID STRING PATHS** - Use typed node references
- **PAST TENSE SIGNALS** - `health_changed` not `health_change`
- **TRAILING COMMAS** - In multi-line arrays/enums/dicts

These standards work together with [Clean Code Standards](./clean-code-standards.md) to create maintainable GDScript code.
