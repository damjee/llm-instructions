# Testing Philosophy: Contract-Driven Testing

**Purpose:** Testing philosophy and practices for maintaining code quality through refactor-safe tests  
**Scope:** Testing strategy, test design, and quality principles  
**Related Documents:** [Clean Code Standards](./clean-code-standards.md), [Godot & GDScript Standards](./godot-gdscript-standards.md)

> **Influences:** Kent Beck (*TDD by Example*), Martin Fowler (*Test Pyramid*), Robert C. Martin (*Clean Code*, Chapter 9: Unit Tests)

---

## Core Mission

Tests exist to:
- **Enable refactoring with confidence** — tests are the safety net for change (Beck, Martin)
- **Protect public contracts** — validate what the code promises to callers
- **Serve as living documentation** — executable specifications of behavior (Beck)
- **Preserve intended invariants** — catch real regressions, not incidental changes

**Golden Rule:** If a change preserves the public contract, tests should continue to pass.

---

## Primary Principle: Test the Contract

> *"Test behavior, not implementation."* — Kent Beck

Only public-facing interfaces and externally observable behavior are first-class test targets.

A module's **contract** — what tests should validate:
- Public functions and their return values
- Public errors and exceptions
- Publicly observable invariants
- Side effects visible to callers

**Implementation details** — private variables, helpers, internal state, call order, and interaction counts — are disposable. If a private value changes but produces the same observable result, the test must pass.

---

## Intent Over Implementation

Tests describe **what must be true**, not **how it is achieved**.

### Focus On

✅ Return values and outputs  
✅ Error conditions and exceptions  
✅ Domain invariants  
✅ Observable state transitions  
✅ Side effects visible to the caller

Avoid coupling tests to call order, interaction counts, private helpers, internal variables, or formatting (unless formatting is part of the contract).

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

Multiple assertions per test are acceptable when they provide diagnostic detail for the same behavior. This trades off against Uncle Bob's "one assert per test" principle (which optimizes for pinpointing exact failures). Use judgment — favor diagnostic clarity when a single behavior has multiple observable aspects.

```python
# ✅ GOOD - Multiple assertions provide diagnostic detail for one behavior
def test_calculate_final_score():
    player = Player(base_score=100, bonus=50, multiplier=2)
    
    score = calculate_final_score(player)
    
    assert score > 0, "Score must be positive"
    assert score == 300, f"Expected 300, got {score}"
    assert score == (100 + 50) * 2, "Formula: (base + bonus) * multiplier"

# ✅ ALSO GOOD - Single assertion when failure is self-evident
def test_calculate_damage_returns_integer():
    damage = calculate_damage(attacker, defender)
    assert isinstance(damage, int)
```

The test is the headline.  
The assertions are the diagnostics.

---

## Determinism First

> *Aligns with the F.I.R.S.T. principle of **Repeatable** tests (Martin) — tests should produce the same results every time, in any environment.*

Testable code favors functional principles: same input → same output, side effects minimized, errors returned explicitly. Difficulty testing often reveals design issues (Beck, Fowler) — testability is a design signal, not just a testing concern.

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

## Dependency Management in Tests

### Prefer Real Behavior Over Mocks

> *"Mocks couple tests to implementation."* — Martin Fowler, *Mocks Aren't Stubs*

Use **fakes** (lightweight implementations that simulate real behavior) over **mocks** (interaction-recording stubs). Fakes keep tests grounded in reality and resilient to refactoring; mocks tend to produce brittle, interaction-based assertions that break when implementation changes.

```python
# ⚠️ FRAGILE - Mock-based test coupled to implementation
def test_process_payment_with_mocks():
    payment_gateway = Mock()
    payment_gateway.charge.return_value = {"status": "success"}
    
    processor = PaymentProcessor(payment_gateway)
    result = processor.process_payment(100)
    
    payment_gateway.charge.assert_called_once_with(100)  # Breaks on refactor
    assert result["status"] == "success"

# ✅ GOOD - Fake simulates real behavior
def test_process_payment_returns_success():
    payment_gateway = FakePaymentGateway()
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

> *Fowler's Test Pyramid: many unit tests at the base, fewer integration tests in the middle, minimal E2E tests at the top.*

**Core business logic should be unit-test dominant.** Push tests as low in the stack as practical without losing essential validation.

Integration tests should be:
- **Few** — only where truly necessary
- **Focused** — test specific boundaries
- **Boundary-level** — test adapters and interfaces

They validate:
- Adapters (HTTP, database, file system)
- Couplers (service-to-service communication)
- External resource interfaces
- Device or persistence boundaries

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

## Testability as a Design Signal

> *"Good design makes testing easier; difficult testing reveals design issues."* — Beck, Fowler

If a module is difficult to test (branch-heavy, edge-case heavy, hard to reason about), that's an architectural signal. Testing should follow architecture, not dictate it — but persistent testing difficulty suggests the module needs clearer boundaries and smaller public contracts.

---

## Snapshot / Golden Test Policy

**Prefer explicit assertions that reveal intent** over golden snapshots. Snapshots can hide what's actually being validated and encourage passive approval rather than deliberate verification.

When the contract involves structured output, assert on the specific elements that matter.

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

**Focus on contract protection, not coverage percentages.**

Coverage metrics can serve as a weak smoke signal, but high coverage does not imply correctness and low coverage does not imply poor design. The real question is: *are the contracts protected?*

---

## Flaky Tests

> *Aligns with F.I.R.S.T. principles: **Independent** and **Repeatable** (Martin) — tests must not depend on each other and must produce consistent results.*

**Flaky tests must be fixed or removed immediately.** A flaky test prevents confident refactoring, reduces trust in the test suite, and undermines the purpose of testing.

Common causes:
- Timing dependencies
- Uncontrolled randomness
- External resource dependencies
- Test order dependencies
- Insufficient cleanup between tests

---

## Property-Based Testing

**Use sparingly.** While invariant-driven testing aligns philosophically with contract testing, prefer explicit test cases for clarity. Property-based testing adds machinery that can obscure intent.

Consider property-based testing when:
1. The invariant is well-defined but the input space is large
2. Explicit test cases can't adequately cover boundary conditions
3. The additional abstraction provides real value over 3-5 explicit cases

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

> *Test code deserves the same quality as production code (Martin), but test readability often takes priority over eliminating repetition (Beck).*

**Favor clarity over abstraction in tests.** Repetition is acceptable when it makes each test self-contained and easy to diagnose. Apply the Rule of Three — only extract shared test logic after it meaningfully repeats three times.

```python
# ✅ GOOD - Explicit, clear, each test tells its own story
def test_add_returns_sum_of_two_positives():
    assert add(2, 3) == 5

def test_add_returns_sum_of_two_negatives():
    assert add(-2, -3) == -5

def test_add_returns_sum_of_positive_and_negative():
    assert add(2, -3) == -1

# ✅ ALSO GOOD - Parametrize when inputs vary but the behavior is identical
# Tradeoff: more concise, but failure messages are less descriptive
@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (-2, -3, -5),
    (2, -3, -1),
])
def test_add(a, b, expected):
    assert add(a, b) == expected
```

---

## Principles at a Glance

In priority order, this philosophy values:

1. **Contract fidelity** — tests validate what the code promises, not how it works
2. **Refactor resilience** — tests survive implementation changes that preserve behavior
3. **Simplicity** — minimal test infrastructure, clear assertions, no unnecessary abstractions
4. **Intentional coverage** — focused testing of meaningful contracts over trivial pass-throughs
5. **Grounded confidence** — trust built through real behavior, not mocks or metrics

---

## Summary

✅ **Tests protect contracts** (Beck: test behavior, not implementation)  
✅ **Internals are disposable** (refactor-safe by design)  
✅ **Intent matters more than mechanics** (what, not how)  
✅ **Determinism enables refactoring** (F.I.R.S.T.: Repeatable)  
✅ **Testability is a design signal** (Beck, Fowler)  
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

## References

- Kent Beck, *Test-Driven Development: By Example*
- Martin Fowler, *Test Pyramid* (martinfowler.com/bliki/TestPyramid.html)
- Martin Fowler, *Mocks Aren't Stubs* (martinfowler.com/articles/mocksArentStubs.html)
- Robert C. Martin, *Clean Code* (Chapter 9: Unit Tests)
