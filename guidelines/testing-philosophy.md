# Testing Philosophy: Behavior-Focused Testing

**Purpose:** Testing philosophy and practices for maintaining code quality through refactor-safe tests  
**Scope:** Testing strategy, test design, and quality principles  
**Related Documents:** [Clean Code Standards](./clean-code-standards.md), [Godot & GDScript Standards](./languages/godot/gdscript-standards.md)
**When to Load:** Load when writing, changing, debugging, or evaluating tests, or when testability is part of the task.  
**Load Order:** Load after Clean Code Standards; use alongside language-specific guidance when relevant.  
**Skip If:** No tests are involved and the task does not affect test behavior or testability.  
**Typical Tasks:** Add tests, fix failing tests, review test quality, improve testability, define testing strategy.

---

## Core Mission

Tests exist to:
- **Enable refactoring with confidence** — tests are the safety net for change
- **Protect public behavior** — validate what the code promises to callers
- **Serve as living documentation** — executable specifications of behavior
- **Preserve intended invariants** — catch real regressions, not incidental changes

**Golden Rule:** If a change preserves the public behavior, tests should continue to pass.

### F.I.R.S.T. Principles

All tests should be:
- **Fast** — run quickly to encourage frequent execution
- **Independent** — no test depends on another test's outcome or order
- **Repeatable** — produce the same results every time, in any environment
- **Self-validating** — pass or fail with no manual interpretation needed
- **Timely** — written close to the code they validate

---

## Test Behavior, Not Implementation

Only public-facing interfaces and externally observable behavior are first-class test targets.

What tests should validate:
- Public functions and their return values
- Public errors and exceptions
- Publicly observable invariants
- Side effects visible to callers

**Implementation details** — private variables, helpers, internal state, call order, and interaction counts — are disposable. If a private value changes but produces the same observable result, the test must pass.

**Example:**

```python
# ✅ GOOD - Tests the contract
def test_dead_attacker_deals_no_damage():
    attacker = Unit(health=0)
    defender = Unit(health=100)

    damage = calculate_damage(attacker, defender)

    assert damage == 0

# ✅ GOOD - Tests error conditions
def test_missing_units_raise_value_error():
    with pytest.raises(ValueError):
        calculate_damage(None, None)

# ❌ BAD - Tests implementation details
def test_damage_calculation_checks_liveness_twice():
    attacker = Mock()
    defender = Mock()

    calculate_damage(attacker, defender)

    assert attacker.is_alive.call_count == 1  # Brittle!
```

---

## Test Structure Philosophy

### Use AAA as the Default Shape

Arrange → Act → Assert.

- **Arrange** — prepare inputs, dependencies, and relevant state
- **Act** — execute the behavior being tested
- **Assert** — verify the outcome

Keep those sections visually distinct with blank lines or brief comments.

The **Act** section should usually be a single logical action. Multiple state-changing steps often mean the test is protecting more than one behavior.

A single test should protect a **single unit of behavior**. Multiple assertions are fine when they diagnose different aspects of that one behavior.

### Test Names Communicate WHAT

Test names should explain the scenario in plain English.

Guidelines:
- Write the name as if you are describing the scenario to someone who knows the problem domain but not the implementation.
- Focus on behavior and outcome, not method names or internal mechanics.
- Use underscores when your framework expects identifier-like names or when they improve readability.
- Utility or helper code can use method-oriented names when that is genuinely the clearest behavior label.

**Examples:**
- `test_dead_attacker_deals_no_damage`
- `test_spawn_fails_when_all_spawn_points_are_full`
- `test_healing_never_pushes_health_above_maximum`
- `test_player_on_cooldown_is_not_ready`

The test name should tell you **what broke**, not which function happened to be called.

### Keep the System Under Test Obvious

Dependencies and the primary subject should be easy to spot.

- Prefer domain names when they make the scenario clearer.
- When a neutral placeholder helps separate the main object from its collaborators, `sut` is an acceptable name for the system under test.
- Avoid hiding key setup in distant helpers, constructors, or global fixtures.

### Assertions Communicate WHY

Assertions explain **why** the invariant failed.

Multiple assertions per test are acceptable when they provide diagnostic detail for the same behavior. Use judgment — favor diagnostic clarity when a single behavior has multiple observable aspects, but recognize that too many assertions can indicate a missing abstraction.

```python
# ✅ GOOD - Multiple assertions diagnose one behavior
def test_bonus_and_multiplier_are_applied_to_final_score():
    player = Player(base_score=100, bonus=50, multiplier=2)

    score = calculate_final_score(player)

    assert score > 0, "Score must be positive"
    assert score == 300, f"Expected 300, got {score}"
    assert score == (100 + 50) * 2, "Formula: (base + bonus) * multiplier"

# ✅ ALSO GOOD - Single assertion when failure is self-evident
def test_damage_is_returned_as_an_integer():
    damage = calculate_damage(attacker, defender)

    assert isinstance(damage, int)
```

The test name is the headline.  
The assertions are the diagnostics.

---

## Determinism First

Tests should produce the same results every time, in any environment.

Testable code favors functional principles: same input → same output, side effects minimized, errors returned explicitly. Tests should focus on return values and explicit errors whenever possible.

Branching logic inside tests is a smell. If a test needs `if` statements to decide what it expects, the scenario is probably too broad or the assertion is too indirect.

```python
# ✅ EXCELLENT - Deterministic, functional
def calculate_damage(attacker: Unit, defender: Unit) -> int:
    if not attacker.is_alive:
        return 0
    if not defender.is_alive:
        return 0
    return max(0, attacker.attack - defender.defense)


def test_dead_attacker_produces_zero_damage():
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

## Setup and Fixture Design

Keep setup visible and local to the scenario.

Prefer small factory/helper methods over constructor-level setup, broad `setUp` / `before_each` hooks, or shared fixtures that hide why the test exists. Reuse is valuable only when it removes noise **without** obscuring the relevant differences between scenarios.

If duplication keeps the setup understandable, accept the duplication. If extraction makes the setup clearer, extract it.

---

## Dependency Management in Tests

### Prefer Real Behavior Over Mocks

Use **fakes** (lightweight implementations that simulate real behavior) over **mocks** (interaction-recording stubs). Fakes keep tests grounded in reality and resilient to refactoring; mocks tend to produce brittle, interaction-based assertions that break when implementation changes.

```python
# ⚠️ FRAGILE - Mock-based test coupled to implementation
def test_payment_processor_calls_the_gateway_once():
    payment_gateway = Mock()
    payment_gateway.charge.return_value = {"status": "success"}

    sut = PaymentProcessor(payment_gateway)
    result = sut.process_payment(100)

    payment_gateway.charge.assert_called_once_with(100)  # Breaks on refactor
    assert result["status"] == "success"

# ✅ GOOD - Fake simulates real behavior
def test_successful_payment_returns_success_status():
    payment_gateway = FakePaymentGateway()
    sut = PaymentProcessor(payment_gateway)

    result = sut.process_payment(100)

    assert result.status == PaymentStatus.SUCCESS
    assert result.amount == 100
```

---

## Integration Testing Strategy

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
def test_saved_user_can_be_loaded_by_email():
    repo = UserRepository(database_url="sqlite:///:memory:")
    user = User(name="Alice", email="alice@example.com")

    repo.save(user)
    retrieved = repo.find_by_email("alice@example.com")

    assert retrieved.name == "Alice"
    assert retrieved.email == "alice@example.com"

# ✅ GOOD - Unit test for business logic
def test_reputation_counts_posts_and_votes():
    user = User(posts=10, upvotes=50, downvotes=5)

    reputation = calculate_reputation(user)

    assert reputation == 55  # posts * 1 + upvotes * 1 - downvotes * 1
```

---

## Testability as a Design Signal

If a module is difficult to test (branch-heavy, edge-case heavy, hard to reason about), that's an architectural signal — testability is a design signal, not just a testing concern. Testing should follow architecture, not dictate it — but persistent testing difficulty suggests the module needs clearer boundaries and smaller public surfaces.

---

## Anti-Patterns to Avoid

### Multiple AAA Sections

Avoid multiple Arrange / Act / Assert cycles in the same test. If a second act/assert pair is required, that usually means you are verifying a second behavior and should split the test.

### Conditional Logic in Tests

Avoid `if` statements and branchy expectation logic inside tests. Tests should remain deterministic and explicit about which scenario they cover.

### Rigid Naming Conventions

Avoid formulaic naming schemes such as `[MethodUnderTest]_[Scenario]_[ExpectedResult]` when they pull attention toward implementation details instead of behavior. Prefer names that read like scenarios.

### High Coupling Between Tests

A change in one test should not affect another. Coupling tests through shared mutable setup, test order, or hidden dependencies violates independence.

### Hidden Setup in Constructors or Shared Hooks

Initializing fixtures in constructors or broad setup hooks can hide context, increase coupling, and reduce readability. Prefer small factory/helper methods when you need reusable setup.

### Overusing Parameterized Tests

Parameterized tests reduce duplication, but they also make individual scenarios less explicit. Use them when the behavior is truly identical across cases; keep high-signal scenarios as named tests.

---

## Additional Testing Guidance

### Snapshot / Golden Test Policy

**Prefer explicit assertions that reveal intent** over golden snapshots. Snapshots can hide what's actually being validated and encourage passive approval rather than deliberate verification. When the contract involves structured output, assert on the specific elements that matter rather than comparing against a stored snapshot.

### Coverage Policy

**Focus on contract protection, not coverage percentages.**

Coverage metrics can serve as a weak smoke signal, but high coverage does not imply correctness and low coverage does not imply poor design. The real question is: *are the contracts protected?*

### Flaky Tests

**Flaky tests must be fixed or removed immediately.** A flaky test prevents confident refactoring, reduces trust in the test suite, and undermines the purpose of testing.

Common causes:
- Timing dependencies
- Uncontrolled randomness
- External resource dependencies
- Test order dependencies
- Insufficient cleanup between tests

### Property-Based Testing

**Use sparingly.** While invariant-driven testing aligns philosophically with contract testing, prefer explicit test cases for clarity. Property-based testing adds machinery that can obscure intent.

Consider property-based testing when:
1. The invariant is well-defined but the input space is large
2. Explicit test cases cannot adequately cover boundary conditions
3. The additional abstraction provides real value over 3-5 explicit cases

### Data Structures and Contract Alignment

**If order is not part of a contract:**
- Tests must not enforce order
- The return structure should reflect that intent (use sets, not lists)
- Or normalization should occur before assertion

Tests must not accidentally define a contract that the API did not intend to define.

---

## DRY in Tests

**Favor clarity over abstraction in tests.** Repetition is acceptable when it keeps each test self-contained, the setup visible, and the scenario easy to diagnose. Apply the Rule of Three before extracting shared logic, and prefer small factory/helper methods over hidden constructor or framework-level setup.

```python
# ✅ GOOD - Explicit, clear, each test tells its own story
def test_two_positive_numbers_add_to_their_sum():
    assert add(2, 3) == 5


def test_two_negative_numbers_add_to_their_sum():
    assert add(-2, -3) == -5


def test_positive_and_negative_numbers_add_correctly():
    assert add(2, -3) == -1

# ✅ ALSO GOOD - Parametrize when inputs vary but the behavior is identical
# Tradeoff: more concise, but individual scenario names are less descriptive
@pytest.mark.parametrize("a,b,expected", [
    (2, 3, 5),
    (-2, -3, -5),
    (2, -3, -1),
])
def test_simple_addition_cases_return_expected_sums(a, b, expected):
    assert add(a, b) == expected
```

---

## Principles at a Glance

In priority order, this philosophy values:

1. **Contract fidelity** — tests validate what the code promises, not how it works
2. **Refactor resilience** — tests survive implementation changes that preserve behavior
3. **Scenario clarity** — names, AAA structure, and assertions make the behavior obvious
4. **Visible setup** — the relevant state and collaborators are easy to see
5. **Intentional coverage** — meaningful contracts matter more than raw percentages
6. **Grounded confidence** — trust built through real behavior, not mocks or metrics

---

## Summary

✅ **Tests protect behavior** (not implementation)  
✅ **Names describe scenarios in plain English**  
✅ **AAA and single-behavior tests keep intent clear**  
✅ **Setup should stay visible and deterministic**  
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
def test_dead_attacker_deals_no_damage():
    attacker = Unit(health=0, attack_power=50, defense=10)
    defender = Unit(health=100, attack_power=30, defense=5)

    damage = calculate_damage(attacker, defender)

    assert damage == 0


def test_dead_defender_takes_no_damage():
    attacker = Unit(health=100, attack_power=50, defense=10)
    defender = Unit(health=0, attack_power=30, defense=5)

    damage = calculate_damage(attacker, defender)

    assert damage == 0


def test_attack_power_over_defense_sets_damage():
    attacker = Unit(health=100, attack_power=50, defense=10)
    defender = Unit(health=100, attack_power=30, defense=5)

    damage = calculate_damage(attacker, defender)

    assert damage == 45  # 50 attack - 5 defense


def test_damage_never_goes_below_zero():
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
    sut = UserService(repo)

    sut.get_user(user_id=1)

    repo.find_by_id.assert_called_once_with(1)  # Brittle!

# ✅ GOOD - Tests observable behavior
def test_existing_user_is_returned_by_id():
    repo = FakeUserRepository()
    repo.add_user(User(id=1, name="Alice"))
    sut = UserService(repo)

    user = sut.get_user(user_id=1)

    assert user.id == 1
    assert user.name == "Alice"
```

---
