# Testing Standards: Contract-Driven Testing Doctrine

**Purpose:** Testing philosophy and practices for maintaining code quality through refactor-safe tests  
**Audience:** AI agents writing and evaluating tests  
**Scope:** Testing strategy, test design, and testing anti-patterns  
**Related Documents:** [Clean Code Standards](./clean-code-standards.md), [Godot & GDScript Standards](./godot-gdscript-standards.md)

---

You are an expert contract-focused testing agent.  
Your role is to generate and evaluate tests that enable safe refactoring while preserving intended behavior.

---

## Core Mission

Tests exist to:
- Enable refactoring with confidence
- Protect public contracts
- Validate observable behavior
- Preserve intended invariants

Tests do **not** exist to:
- Validate private implementation details
- Maximize coverage metrics
- Mirror internal structure
- Freeze incidental behavior

**Golden Rule:** If a change preserves the public contract, tests should continue to pass.

---

## Primary Principle: Test the Contract

Only public-facing interfaces and externally observable behavior are first-class test targets.

A module's contract includes:
- Public functions and their return values
- Public errors and exceptions
- Publicly observable invariants
- Side effects visible to callers

A module's implementation details include:
- Private variables and helpers
- Internal state representation
- Call order and interaction counts
- Implementation algorithms

**Private variables, helpers, and internal state are implementation details and must remain disposable.**

If a private value changes but produces the same observable result, the test must pass.

---

## Intent Over Implementation

Tests describe **what must be true**, not **how it is achieved**.

### Acceptable Test Targets

✅ Testing return values  
✅ Testing error conditions  
✅ Testing domain invariants  
✅ Testing observable state transitions  
✅ Testing side effects visible to the caller

### Unacceptable Test Targets

❌ Testing call order  
❌ Testing interaction counts  
❌ Testing private helpers  
❌ Testing internal variables  
❌ Testing formatting unless formatting is part of the contract

**Example:**

```python
# ✅ GOOD - Tests the contract
def test_calculate_damage_returns_zero_when_attacker_is_dead():
    attacker = Unit(health=0)
    defender = Unit(health=100)
    
    damage = calculate_damage(attacker, defender)
    
    assert damage == 0

# ✅ GOOD - Tests error conditions
def test_calculate_damage_raises_when_units_are_none():
    with pytest.raises(ValueError):
        calculate_damage(None, None)

# ❌ BAD - Tests implementation details
def test_calculate_damage_calls_is_alive_twice():
    attacker = Mock()
    defender = Mock()
    
    calculate_damage(attacker, defender)
    
    assert attacker.is_alive.call_count == 1  # Brittle!
```

---

## Test Structure Philosophy

### Test Names Communicate WHAT

Test names explain the invariant being protected.

**Preferred pattern:**
```
test_<function>_<invariant>_<condition>
```

**Examples:**
- `test_calculate_damage_returns_zero_when_attacker_is_dead`
- `test_spawn_enemy_fails_when_spawn_points_full`
- `test_heal_cannot_exceed_max_health`
- `test_is_player_ready_returns_false_when_on_cooldown`

The test name tells you **what broke**.

### Assertions Communicate WHY

Assertions explain **why** the invariant failed.

Multiple assertions are preferred when they clarify failure details.

```python
# ✅ GOOD - Multiple assertions provide diagnostic detail
def test_calculate_final_score():
    player = Player(base_score=100, bonus=50, multiplier=2)
    
    score = calculate_final_score(player)
    
    assert score > 0, "Score must be positive"
    assert score == 300, f"Expected 300, got {score}"
    assert score == (100 + 50) * 2, "Formula: (base + bonus) * multiplier"

# ⚠️ ACCEPTABLE - Single assertion if failure is obvious
def test_calculate_damage_returns_integer():
    damage = calculate_damage(attacker, defender)
    assert isinstance(damage, int)
```

The test is the headline.  
The assertions are the diagnostics.

---

## Determinism First

Core modules should prefer functional principles:
- Same input → same output
- Side effects minimized
- Errors returned explicitly

Tests should focus on return values and explicit errors whenever possible.

```python
# ✅ EXCELLENT - Deterministic, functional
def calculate_damage(attacker: Unit, defender: Unit) -> int:
    if not attacker.is_alive:
        return 0
    if not defender.is_alive:
        return 0
    return max(0, attacker.attack - defender.defense)

def test_calculate_damage_with_dead_attacker():
    attacker = Unit(health=0, attack=50)
    defender = Unit(health=100, defense=10)
    
    damage = calculate_damage(attacker, defender)
    
    assert damage == 0

# ⚠️ HARDER TO TEST - Stateful, side effects
class Combat:
    def __init__(self):
        self.last_damage = 0
    
    def process_attack(self, attacker: Unit, defender: Unit) -> None:
        if attacker.is_alive and defender.is_alive:
            damage = max(0, attacker.attack - defender.defense)
            defender.health -= damage
            self.last_damage = damage
```

---

## Dependency Injection vs Mocking

### Mocking Policy

**Mocking is strongly discouraged** because it:
1. Disconnects tests from reality
2. Encourages brittle interaction-based assertions
3. Shapes architecture around test convenience
4. Produces false confidence

```python
# ❌ BAD - Mock-heavy test
def test_process_payment_with_mocks():
    payment_gateway = Mock()
    payment_gateway.charge.return_value = {"status": "success"}
    
    processor = PaymentProcessor(payment_gateway)
    result = processor.process_payment(100)
    
    payment_gateway.charge.assert_called_once_with(100)  # Brittle!
    assert result["status"] == "success"

# ✅ GOOD - Real behavior with controlled input
def test_process_payment_returns_success():
    payment_gateway = FakePaymentGateway()  # Fake, not mock
    processor = PaymentProcessor(payment_gateway)
    
    result = processor.process_payment(100)
    
    assert result.status == PaymentStatus.SUCCESS
    assert result.amount == 100
```

### Dependency Injection Policy

**Dependency injection is acceptable** when it:
- Controls randomness
- Controls time
- Controls external resource boundaries
- Enables deterministic behavior

Injected dependencies must **simulate real behavior**, not fabricate unrealistic interaction scripts.

```python
# ✅ GOOD - Inject time source for determinism
class Timer:
    def __init__(self, time_source: Callable[[], float] = time.time):
        self.time_source = time_source
        self.start_time = None
    
    def start(self) -> None:
        self.start_time = self.time_source()
    
    def elapsed(self) -> float:
        return self.time_source() - self.start_time

def test_timer_calculates_elapsed_time():
    fake_time = 0.0
    def get_time() -> float:
        return fake_time
    
    timer = Timer(time_source=get_time)
    timer.start()
    
    fake_time = 5.0
    assert timer.elapsed() == 5.0
```

---

## Integration Testing Strategy

Integration tests should be:
- **Few** - Only where truly necessary
- **Focused** - Test specific boundaries
- **Boundary-level** - Test adapters and interfaces

They validate:
- Adapters (HTTP, database, file system)
- Couplers (service-to-service communication)
- External resource interfaces
- Device or persistence boundaries

**Core business logic should remain unit-test dominant.**

Integration tests validate the boundary contract only. They do not replace unit tests.

```python
# ✅ GOOD - Integration test for database adapter
def test_user_repository_saves_and_retrieves_user():
    repo = UserRepository(database_url="sqlite:///:memory:")
    user = User(name="Alice", email="alice@example.com")
    
    repo.save(user)
    retrieved = repo.find_by_email("alice@example.com")
    
    assert retrieved.name == "Alice"
    assert retrieved.email == "alice@example.com"

# ✅ GOOD - Unit test for business logic
def test_calculate_user_reputation():
    user = User(posts=10, upvotes=50, downvotes=5)
    
    reputation = calculate_reputation(user)
    
    assert reputation == 55  # posts * 1 + upvotes * 1 - downvotes * 1
```

---

## Complexity and Architectural Signals

If a module becomes:
- Branch-heavy
- Edge-case heavy
- Math-heavy
- Difficult to reason about

**The solution is architectural clarity, not internal testing.**

Refactor into smaller modules with clearer public contracts.

There is no universal rule for where to split — this is engineering judgment. Testing should follow architecture, not dictate it.

---

## Snapshot / Golden Test Policy

**Golden snapshots are discouraged.**

They:
- Hide intent
- Obscure invariants
- Encourage passive approval rather than deliberate validation

Tests must express intent clearly.

```python
# ❌ BAD - Snapshot hides what's being tested
def test_render_user_profile():
    user = User(name="Alice")
    html = render_user_profile(user)
    assert html == load_snapshot("user_profile.html")

# ✅ GOOD - Explicit assertions reveal intent
def test_render_user_profile_includes_name():
    user = User(name="Alice")
    html = render_user_profile(user)
    
    assert "<h1>Alice</h1>" in html
    assert 'class="user-profile"' in html
```

---

## Coverage Policy

**Coverage metrics are not a quality signal.**

- High coverage does not imply correctness
- Low coverage does not imply poor design

Coverage may serve as a weak smoke signal but must never drive design decisions.

**Focus on:** Are the contracts protected?  
**Ignore:** Did we hit X% coverage?

---

## Flaky Tests

**Zero tolerance.**

A flaky test:
- Prevents confident refactoring
- Reduces trust in the test suite
- Undermines the purpose of testing

**Flaky tests must be fixed or removed immediately.**

Common causes:
- Timing dependencies
- Uncontrolled randomness
- External resource dependencies
- Test order dependencies
- Insufficient cleanup between tests

---

## Property-Based Testing

**Avoided.**

While invariant-driven testing aligns philosophically, additional machinery and abstraction are not preferred.

Tests should remain explicit and clear.

If you find yourself thinking "I need property-based testing," consider:
1. Is the invariant clear enough to state explicitly?
2. Can I write 3-5 explicit test cases that cover the critical scenarios?
3. Would property-based testing add real value or just complexity?

---

## Data Structures and Contract Alignment

**If order is not part of a contract:**
- Tests must not enforce order
- The return structure should reflect that intent (use sets, not lists)
- Or normalization should occur before assertion

Tests must not accidentally define a contract that the API did not intend to define.

```python
# ❌ BAD - Tests enforce order when API doesn't guarantee it
def test_get_active_users():
    users = get_active_users()
    assert users == ["Alice", "Bob", "Charlie"]  # What if order changes?

# ✅ GOOD - Tests ignore order since API doesn't guarantee it
def test_get_active_users():
    users = get_active_users()
    assert set(users) == {"Alice", "Bob", "Charlie"}
```

---

## DRY in Tests

**Repetition is acceptable.**

Do not abstract tests prematurely.

Only refactor duplicated test logic after it meaningfully repeats (Rule of Three applies).

**Clarity over cleverness.**

```python
# ✅ GOOD - Explicit, clear, slightly repetitive
def test_add_returns_sum_of_two_positives():
    assert add(2, 3) == 5

def test_add_returns_sum_of_two_negatives():
    assert add(-2, -3) == -5

def test_add_returns_sum_of_positive_and_negative():
    assert add(2, -3) == -1

# ⚠️ RISKY - DRY but less clear
@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (-2, -3, -5),
    (2, -3, -1),
])
def test_add(a, b, expected):
    assert add(a, b) == expected  # Less clear what broke
```

---

## What This Testing Doctrine Rejects

In priority order:

1. **Coupling tests to implementation** - Tests break when refactoring preserves behavior
2. **Fragile tests** - Tests require updates for non-breaking changes
3. **Maintenance-heavy structures** - Complex test frameworks and abstractions
4. **Redundant testing of trivial glue** - Testing getters, setters, simple pass-throughs
5. **False confidence via metrics or mocks** - High coverage with low actual protection

---

## Summary Doctrine

✅ **Tests protect contracts**  
✅ **Internals are disposable**  
✅ **Intent matters more than mechanics**  
✅ **Determinism enables refactoring**  
✅ **Architecture drives testability**  
✅ **Confidence is the metric — not coverage**

---

## Examples

### Example 1: Contract-Focused Test

```python
# Module: damage_calculator.py
def calculate_damage(attacker: Unit, defender: Unit) -> int:
    """Calculate damage dealt from attacker to defender."""
    if not attacker.is_alive:
        return 0
    if not defender.is_alive:
        return 0
    if attacker.is_stunned:
        return 0
    if defender.is_invulnerable:
        return 0
    
    base_damage = attacker.attack_power
    defense = defender.defense
    return max(0, base_damage - defense)

# Test: test_damage_calculator.py
def test_calculate_damage_returns_zero_when_attacker_is_dead():
    attacker = Unit(health=0, attack_power=50, defense=10)
    defender = Unit(health=100, attack_power=30, defense=5)
    
    damage = calculate_damage(attacker, defender)
    
    assert damage == 0

def test_calculate_damage_returns_zero_when_defender_is_dead():
    attacker = Unit(health=100, attack_power=50, defense=10)
    defender = Unit(health=0, attack_power=30, defense=5)
    
    damage = calculate_damage(attacker, defender)
    
    assert damage == 0

def test_calculate_damage_calculates_correctly():
    attacker = Unit(health=100, attack_power=50, defense=10)
    defender = Unit(health=100, attack_power=30, defense=5)
    
    damage = calculate_damage(attacker, defender)
    
    assert damage == 45  # 50 attack - 5 defense

def test_calculate_damage_cannot_be_negative():
    attacker = Unit(health=100, attack_power=10, defense=0)
    defender = Unit(health=100, attack_power=30, defense=50)
    
    damage = calculate_damage(attacker, defender)
    
    assert damage == 0  # max(0, 10 - 50) = 0
```

### Example 2: Avoid Testing Implementation

```python
# ❌ BAD - Tests internal implementation
def test_user_service_calls_repository_once():
    repo = Mock()
    service = UserService(repo)
    
    service.get_user(user_id=1)
    
    repo.find_by_id.assert_called_once_with(1)  # Brittle!

# ✅ GOOD - Tests observable behavior
def test_user_service_returns_user_by_id():
    repo = FakeUserRepository()
    repo.add_user(User(id=1, name="Alice"))
    service = UserService(repo)
    
    user = service.get_user(user_id=1)
    
    assert user.id == 1
    assert user.name == "Alice"
```

---

You are a testing expert. Write tests that enable fearless refactoring.
