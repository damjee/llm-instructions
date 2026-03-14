# Clean Code Standards

**Purpose:** Language-agnostic clean code principles for writing and improving code  
**Scope:** General principles applicable across programming languages  
**Related Documents:** [Godot & GDScript Standards](./languages/godot/gdscript-standards.md), [Testing Philosophy](./testing-philosophy.md)
**When to Load:** Load for any task that changes, reviews, or designs source code.  
**Load Order:** Load this first; it is the baseline for all other guides.  
**Skip If:** The task is unrelated to code changes or code quality.  
**Typical Tasks:** New implementation, refactoring, code review, bug fixing, API design.

> **Note:** Examples use Python for illustration; apply equivalent patterns in your target language.

---

## Core Qualities

Code should be:
- **Readable** - Clear intent through naming and structure
- **Maintainable** - Easy to modify and extend
- **Consistent** - Follows conventions consistently

## Focus Areas

- Function structure (early returns, single responsibility)
- Variable and function naming
- Code organization and order
- Formatting and whitespace
- Type annotations
- Self-documenting code over comments

---

## Clean Code Principles

### SOLID Principles

- **Single Responsibility:** Each class/function has one clear purpose
- **Open/Closed:** Extend through inheritance/composition, not modification
- **Liskov Substitution:** Derived classes are substitutable for base classes
- **Interface Segregation:** Small focused interfaces
- **Dependency Inversion:** Depend on abstractions not concrete implementations

### Clean Code Patterns

- **KISS (Keep It Simple):** Simple solutions for simple problems, no over-engineering
- **YAGNI (You Aren't Gonna Need It):** Only build what's needed NOW, not "just in case" features
- **DRY (Don't Repeat Yourself):** Abstract duplication after 3rd occurrence (Rule of Three)
- **TDA (Tell, Don't Ask):** Objects encapsulate behavior with data

---

## Function Design

### Size and Complexity
- **Keep functions short** — if a function exceeds ~20 lines, consider whether it has distinct concerns worth extracting
- **Single purpose per function**
- **Single level of abstraction per function**
- **Meaningful names over comments**

### Key Practice: Early Returns (No Deep Nesting)

**Use early returns to avoid nesting:**

```python
# ✅ EXCELLENT - Early returns
def calculate_damage(attacker, defender):
    if not attacker.is_alive():
        return 0
    
    if not defender.is_alive():
        return 0
    
    if attacker.is_stunned():
        return 0
    
    if defender.is_invulnerable():
        return 0
    
    base_damage = attacker.attack_power
    defense = defender.defense
    return max(0, base_damage - defense)

# ❌ BAD - Deep nesting
def calculate_damage(attacker, defender):
    damage = 0
    if attacker.is_alive():
        if defender.is_alive():
            if not attacker.is_stunned():
                if not defender.is_invulnerable():
                    base_damage = attacker.attack_power
                    defense = defender.defense
                    damage = max(0, base_damage - defense)
    return damage
```

**Why early returns matter:**
- Reduces cognitive load
- Eliminates arrow anti-pattern
- Makes happy path clear
- Reduces indentation levels
- Easier to reason about edge cases

---

## Naming Conventions

### Function Names

- **Functions should clearly express their purpose**
- **Avoid abbreviations and cryptic names**
- **Names should reveal intent**

```python
# ✅ GOOD - Clear intent
def calculate_total_score():
def spawn_enemy_at_position(pos):
def check_collision():
def update_health_display():

# ❌ BAD - Unclear names
def score():  # What does this do?
def proc(x):  # Cryptic
def do_stuff():  # Vague
```

### Variable Naming

- **Descriptive names reveal intent**
- **Avoid abbreviations unless universally known**
- **Boolean variables should read like questions:** `is_active`, `has_permission`, `can_proceed`
- **Collections should be plural:** `items`, `enemies`, `players`

```python
# ✅ GOOD - Clear intent
player_health = 100
enemy_count = 5
is_game_over = False
active_weapons = []

# ❌ BAD - Unclear or abbreviated
hp = 100
cnt = 5
flag = False
arr = []
```

---

## Type Safety and Clarity

### Avoid Hungarian Notation

**Variable names should describe purpose, not type:**

```python
# ✅ GOOD - Descriptive names
player
enemy_count
spawn_timer
health_bar

# ❌ BAD - Hungarian notation
player_obj
int_enemy_count
timer_spawn
str_player_name
```

**Note:** When using static typing (in languages that support it), type information belongs in type annotations, not variable names.

---

## Self-Documenting Code

### Key Practice: Code Clarity Over Comments

**Code clarity through naming and structure, NOT comments:**

```python
# ✅ EXCELLENT - Self-documenting
def is_player_ready_to_attack() -> bool:
    return has_weapon() and not is_on_cooldown() and has_enough_mana()

def calculate_critical_hit_damage(base_damage: int) -> int:
    CRITICAL_MULTIPLIER: float = 2.0
    return int(base_damage * CRITICAL_MULTIPLIER)

# ❌ BAD - Needs comments
def check():  # Check if player can attack
    return hw() and not cd() and mana > 0  # Has weapon, not on cooldown, has mana

def calc(dmg):  # Calculate critical damage
    return int(dmg * 2.0)  # Double damage for crits
```

**When code needs comments, it needs refactoring instead:**
- Extract complex expressions into well-named functions
- Use constants with descriptive names for magic numbers
- Break down complex logic into smaller, named steps

**Exception:** API documentation comments are acceptable when generating documentation for public libraries.

---

## Anti-Patterns to Fix

### Fix Deep Nesting
Convert nested if statements to early returns.

### Fix Long Functions
Extract distinct concerns into separate functions.

### Fix Magic Numbers
Replace with named constants:

```python
# ❌ Before
if health > 50:
    do_something()

# ✅ After
CRITICAL_HEALTH_THRESHOLD: int = 50
if health > CRITICAL_HEALTH_THRESHOLD:
    do_something()
```

### Fix Poor Names
Make names reveal intent:

```python
# ❌ Before
def proc(x):
    pass

# ✅ After
def process_user_input(input_event: InputEvent) -> None:
    pass
```

### Fix Comments
Remove comments and make code self-documenting through better naming and structure.

---

## Examples

### Example 1: Early Returns

**Before:**
```python
def process_attack(attacker, target):
    damage = 0
    if attacker != None:
        if target != None:
            if attacker.alive:
                if not attacker.stunned:
                    damage = attacker.atk - target.def
                    target.hp -= damage
    return damage
```

**After:**
```python
def calculate_and_apply_damage(attacker: Unit, target: Unit) -> int:
    if attacker is None:
        return 0
    
    if target is None:
        return 0
    
    if not attacker.is_alive:
        return 0
    
    if attacker.is_stunned:
        return 0
    
    damage: int = attacker.attack_power - target.defense
    target.current_health -= damage
    return damage
```

### Example 2: Clear Naming

**Before:**
```python
def score():
    total = 0
    for i in items:
        total += i.val
    return total

def enemy(pos):
    e = EnemyScene.instantiate()
    e.position = pos
    add_child(e)
    return e
```

**After:**
```python
def calculate_total_score():
    total = 0
    for item in items:
        total += item.value
    return total

def spawn_enemy_at_position(position):
    enemy = enemy_scene.instantiate()
    enemy.position = position
    add_child(enemy)
    return enemy
```

### Example 3: Self-Documenting Code

**Before:**
```python
# Check if the player has enough resources to build
def can_build():
    return gold >= 100 and wood >= 50  # Need 100 gold and 50 wood

# Apply damage reduction based on armor
def calc_dmg(dmg):
    return dmg * (1.0 - armor * 0.01)  # Each armor point reduces 1%
```

**After:**
```python
def has_sufficient_resources_to_build() -> bool:
    REQUIRED_GOLD: int = 100
    REQUIRED_WOOD: int = 50
    return gold >= REQUIRED_GOLD and wood >= REQUIRED_WOOD

def calculate_damage_after_armor_reduction(base_damage: int) -> int:
    ARMOR_REDUCTION_PERCENT: float = 0.01
    damage_multiplier: float = 1.0 - (armor * ARMOR_REDUCTION_PERCENT)
    return int(base_damage * damage_multiplier)
```

---

## Summary

- **EARLY RETURNS** - Eliminate nesting
- **CLEAR NAMES** - Functions and variables reveal intent
- **NO HUNGARIAN NOTATION** - Variable names describe purpose, not type
- **SELF-DOCUMENTING** - Code clarity over comments
- **SMALL FUNCTIONS** - Keep functions focused on a single concern
