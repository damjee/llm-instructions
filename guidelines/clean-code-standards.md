# Clean Code Standards

**Purpose:** Language-agnostic clean code rules for writing and improving code  
**Scope:** General principles that should apply across programming languages and projects  
**Related Documents:** [Godot & GDScript Standards](./languages/godot/gdscript-standards.md), [Testing Philosophy](./testing-philosophy.md)  
**When to Load:** Load for any task that changes, reviews, or designs source code.  
**Load Order:** Load this first; it is the baseline for all other guides.  
**Skip If:** The task is unrelated to code changes or code quality.  
**Typical Tasks:** New implementation, refactoring, code review, bug fixing, API design.

> **Note:** Examples use Python for illustration; apply the same patterns in the target language.

---

## Primary Goal

Produce code that is readable, maintainable, consistent, and safe to change.

## Operating Defaults

- Preserve behavior while refactoring.
- Prefer clarity over cleverness.
- Follow the local project and language conventions consistently.
- Optimize for the next real change, not hypothetical future work.
- Make intent obvious at the point of use.

## Non-Negotiable Rules

### 1. Keep units small and focused

- Give each function, method, and class one clear responsibility.
- Keep one level of abstraction per function.
- Split validation, orchestration, transformation, and I/O when they compete for attention.
- If a function grows beyond roughly 20 lines, check whether distinct concerns should be extracted.

**✅ Good**

```python
def create_player(request):
    validate_create_player_request(request)

    player = build_player_from_request(request)
    player_repository.save(player)
    return player


def validate_create_player_request(request):
    if not request.name:
        raise ValueError("name is required")


def build_player_from_request(request):
    return Player(name=request.name, class_name=request.class_name)
```

**❌ Bad**

```python
def create_player(request):
    if not request.name:
        raise ValueError("name is required")

    player = Player()
    player.name = request.name
    player.class_name = request.class_name
    player_repository.save(player)

    return {
        "name": player.name,
        "class_name": player.class_name,
    }
```

### 2. Use guard clauses instead of deep nesting

- Check invalid or edge cases first.
- Keep the happy path at the lowest indentation level.
- Prefer early returns over nested conditionals.
- Do not carry placeholder variables only to return them later.

**✅ Good**

```python
def calculate_damage(attacker, defender):
    if not attacker.is_alive():
        return 0

    if not defender.is_alive():
        return 0

    if attacker.is_stunned():
        return 0

    if defender.is_invulnerable():
        return 0

    return max(0, attacker.attack_power - defender.defense)
```

**❌ Bad**

```python
def calculate_damage(attacker, defender):
    damage = 0
    if attacker.is_alive():
        if defender.is_alive():
            if not attacker.is_stunned():
                if not defender.is_invulnerable():
                    damage = max(0, attacker.attack_power - defender.defense)
    return damage
```

### 3. Make names reveal intent

- Use verb-based names for behavior.
- Use descriptive nouns for data.
- Avoid abbreviations unless they are universally understood in the domain.
- Name booleans as predicates such as `is_ready`, `has_permission`, or `can_retry`.
- Name collections in plural form.

**✅ Good**

```python
def calculate_total_score(items):
    total_score = 0
    for item in items:
        total_score += item.value
    return total_score


is_retry_allowed = True
active_players = []
```

**❌ Bad**

```python
def score(xs):
    total = 0
    for x in xs:
        total += x.val
    return total


flag = True
arr = []
```

### 4. Put type information in the type system

- Names describe purpose, not representation.
- Do not use Hungarian notation or type prefixes or suffixes.
- When the language supports typing, express type information in annotations or declarations.

**✅ Good**

```python
def find_player_by_id(player_id: str) -> Player | None:
    return player_repository.find(player_id)


player_name: str = "Avery"
enemy_count: int = 3
```

**❌ Bad**

```python
def find_player_by_id(str_player_id):
    return player_repository.find(str_player_id)


str_player_name = "Avery"
int_enemy_count = 3
```

### 5. Prefer self-documenting code over explanatory comments

- Use well-named functions, variables, parameters, and constants.
- Replace magic numbers with named constants.
- Break complex logic into named steps.
- Do not use comments to compensate for unclear code.
- Reserve comments for public API docs or unavoidable external constraints.

**✅ Good**

```python
CRITICAL_HEALTH_THRESHOLD = 25
CRITICAL_DAMAGE_MULTIPLIER = 2.0


def is_player_in_critical_condition(current_health):
    return current_health <= CRITICAL_HEALTH_THRESHOLD


def calculate_critical_hit_damage(base_damage):
    return int(base_damage * CRITICAL_DAMAGE_MULTIPLIER)
```

**❌ Bad**

```python
def check(hp):  # Check whether the player is in danger
    return hp <= 25


def calc(dmg):  # Double damage for critical hits
    return int(dmg * 2.0)
```

### 6. Keep structure predictable

- Group related behavior together.
- Use consistent ordering and formatting within the project or language style guide.
- Keep public entrypoints easy to find and private helpers secondary.
- Avoid unnecessary indirection.

**✅ Good**

```python
class ScoreService:
    def calculate_total_score(self, items):
        validated_items = self._filter_scored_items(items)
        return self._sum_scores(validated_items)

    def _filter_scored_items(self, items):
        return [item for item in items if item.is_scored]

    def _sum_scores(self, items):
        return sum(item.value for item in items)
```

**❌ Bad**

```python
class ScoreService:
    def _sum_scores(self, items):
        return sum(item.value for item in items)

    def calculate_total_score(self, items):
        validated_items = self._filter_scored_items(items)
        return self._sum_scores(validated_items)

    def _filter_scored_items(self, items):
        return [item for item in items if item.is_scored]
```

## Refactoring Patterns

Use these transformations when cleaning existing code:

### Replace nested conditionals with guard clauses

**⬅️ Before**

```python
def process_attack(attacker, target):
    damage = 0
    if attacker is not None:
        if target is not None:
            if attacker.is_alive:
                if not attacker.is_stunned:
                    damage = attacker.attack_power - target.defense
                    target.current_health -= damage
    return damage
```

**➡️ After**

```python
def calculate_and_apply_damage(attacker, target):
    if attacker is None:
        return 0

    if target is None:
        return 0

    if not attacker.is_alive:
        return 0

    if attacker.is_stunned:
        return 0

    damage = attacker.attack_power - target.defense
    target.current_health -= damage
    return damage
```

### Replace vague names with intent-revealing names

**⬅️ Before**

```python
def proc(x):
    if x.ok:
        return x.val
    return 0
```

**➡️ After**

```python
def calculate_processed_value(task_result):
    if not task_result.is_successful:
        return 0

    return task_result.value
```

### Replace magic numbers with named constants

**⬅️ Before**

```python
def can_build_structure(gold, wood):
    return gold >= 100 and wood >= 50
```

**➡️ After**

```python
REQUIRED_GOLD_TO_BUILD = 100
REQUIRED_WOOD_TO_BUILD = 50


def can_build_structure(gold, wood):
    return (
        gold >= REQUIRED_GOLD_TO_BUILD
        and wood >= REQUIRED_WOOD_TO_BUILD
    )
```

### Replace comment-driven code with named steps

**⬅️ Before**

```python
def handle_checkout(cart):
    # Validate the cart before charging the user
    if not cart.items:
        raise ValueError("cart cannot be empty")

    # Apply tax and discount rules
    total = cart.subtotal * 1.08
    if cart.has_discount:
        total -= 10

    # Charge the customer
    payment_gateway.charge(cart.user_id, total)
```

**➡️ After**

```python
def handle_checkout(cart):
    validate_cart(cart)
    total = calculate_checkout_total(cart)
    payment_gateway.charge(cart.user_id, total)


def validate_cart(cart):
    if not cart.items:
        raise ValueError("cart cannot be empty")


def calculate_checkout_total(cart):
    subtotal_with_tax = cart.subtotal * 1.08

    if not cart.has_discount:
        return subtotal_with_tax

    return subtotal_with_tax - 10
```

## Design Heuristics

- **SOLID:** separate responsibilities, extend safely, substitute implementations, keep interfaces focused, and depend on abstractions.
- **KISS:** choose the simplest solution that solves the current problem.
- **YAGNI:** do not build speculative features.
- **DRY:** remove true duplication once a pattern is proven, not before.
- **Tell, Don't Ask:** keep behavior close to the data it acts on.

## Refactoring Priorities

When cleaning existing code, fix issues in this order:

1. Deep nesting
2. Mixed responsibilities
3. Vague names
4. Magic numbers
5. Explanatory comments hiding unclear code

## Quick Adherence Check

Before finishing a change, verify:

- Is the main path easy to follow?
- Does every important name reveal intent?
- Are types expressed through the language rather than the identifier?
- Are repeated values or rules named?
- Can a reader understand the code without explanatory comments?
- Did the change preserve behavior?

## Summary

- Keep code small, clear, and focused.
- Prefer guard clauses over nesting.
- Make names do the explaining.
- Put type info in the type system.
- Use comments sparingly.
- Refactor toward simpler structure, not more machinery.
