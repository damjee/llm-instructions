# Testing Philosophy: Behavior-Focused Testing

**Purpose:** Testing philosophy and practices for maintaining code quality through refactor-safe tests  
**Scope:** Testing strategy, test design, and quality principles  
**When to Load:** Load when writing, changing, debugging, or evaluating tests, or when testability is part of the task.  
**Load Order:** Load after Clean Code Standards; use alongside language-specific guidance when relevant.  
**Skip If:** No tests are involved and the task does not affect test behavior or testability.  
**Typical Tasks:** Add tests, fix failing tests, review test quality, improve testability, define testing strategy.

> **Note:** Examples use Python for illustration; apply the same patterns in the target language and test framework.

---

## Primary Goal

Use tests to protect behavior and make refactoring safe.

**Golden Rule:** If a change preserves the public behavior, the tests should continue to pass.

## Operating Defaults

- Test public contracts and caller-visible behavior.
- Prefer tests that are fast, independent, repeatable, self-validating, and timely.
- Treat tests as executable specifications of behavior.
- Prefer real behavior over synthetic test machinery when practical.
- Optimize for confidence, not coverage percentages.

## Non-Negotiable Rules

### 1. Test behavior, not implementation

- Assert on public outputs, public errors, observable state, and caller-visible side effects.
- Do not assert on private state, helper calls, call counts, or call order unless that interaction is itself the contract.
- If implementation changes without changing behavior, the test should still pass.

**✅ Good**

```python
def test_dead_attacker_deals_no_damage():
    attacker = Unit(health=0, attack=50)
    defender = Unit(health=100, defense=10)

    damage = calculate_damage(attacker, defender)

    assert damage == 0
```

**❌ Bad**

```python
def test_damage_calculator_checks_liveness_before_applying_damage():
    attacker = Mock()
    defender = Mock()

    calculate_damage(attacker, defender)

    attacker.is_alive.assert_called_once()
    defender.is_alive.assert_called_once()
```

### 2. Keep each test focused on one behavior

- A test should protect one unit of behavior.
- Use Arrange -> Act -> Assert as the default shape.
- Usually keep one logical action in the Act section.
- Multiple assertions are acceptable when they diagnose different aspects of the same behavior.
- If a test needs multiple act/assert cycles, it probably covers too much.

**✅ Good**

```python
def test_checkout_applies_discount_to_total():
    cart = Cart(subtotal=100, discount=10)

    total = calculate_checkout_total(cart)

    assert total == 90
```

**❌ Bad**

```python
def test_checkout_flow():
    cart = Cart(subtotal=100, discount=10)

    total = calculate_checkout_total(cart)
    assert total == 90

    cart.add_item(Item(price=25))
    total = calculate_checkout_total(cart)
    assert total == 115
```

### 3. Name scenarios, not mechanics

- Test names should read like behavior descriptions in plain language.
- Prefer names that still make sense after refactoring.
- Avoid rigid templates that force implementation-first thinking.
- The test name should tell what broke, not which method happened to run.

**✅ Good**

```python
def test_healing_never_pushes_health_above_maximum():
    player = Player(current_health=95, maximum_health=100)

    player.heal(20)

    assert player.current_health == 100
```

**❌ Bad**

```python
def test_apply_heal_100_95_20():
    player = Player(current_health=95, maximum_health=100)

    player.heal(20)

    assert player.current_health == 100
```

### 4. Keep setup visible and local

- Make the relevant setup easy to see in the test.
- Prefer small factory or helper functions over broad shared setup.
- Avoid hiding scenario-defining state in constructors, `setUp`, `before_each`, or global fixtures.
- Accept some duplication when it keeps the scenario obvious.
- The system under test does not need a domain-specific variable name in every case.
- When a neutral placeholder keeps the main subject clear and separates it from collaborators, `sut` is a good name.

**✅ Good**

```python
def test_overdue_invoice_is_marked_past_due():
    invoice = Invoice(total=100, due_date=date(2026, 4, 1), paid=False)

    invoice.mark_past_due_if_needed(today=date(2026, 4, 5))

    assert invoice.status == InvoiceStatus.PAST_DUE
```

**✅ Also good when `sut` keeps the main subject obvious**

```python
def test_registered_user_receives_welcome_message():
    email_sender = FakeEmailSender()
    sut = RegistrationService(email_sender=email_sender)

    sut.register_user("alice@example.com")

    assert email_sender.sent_messages == [
        EmailMessage(to="alice@example.com", subject="Welcome")
    ]
```

**❌ Bad**

```python
def test_overdue_invoice_is_marked_past_due():
    invoice = make_default_invoice()

    invoice.mark_past_due_if_needed(today=test_clock.today())

    assert invoice.status == InvoiceStatus.PAST_DUE
```

### 5. Favor determinism

- The same test should produce the same result in any environment.
- Avoid conditional logic inside tests.
- Control time, randomness, ordering, and external dependencies.
- Prefer explicit return values, state changes, and errors over implicit side effects.

**✅ Good**

```python
def test_token_expires_after_five_minutes():
    clock = FakeClock(now=datetime(2026, 4, 5, 12, 0, 0))
    token = SessionToken(created_at=clock.now())

    clock.advance(minutes=6)

    assert token.is_expired(now=clock.now()) is True
```

**❌ Bad**

```python
def test_token_eventually_expires():
    token = SessionToken(created_at=datetime.utcnow())

    time.sleep(301)

    assert token.is_expired(now=datetime.utcnow()) is True
```

### 6. Prefer assertions in this order

1. **Output-based** — assert on return values or produced output; prefer this for pure logic and calculations
2. **State-based** — assert on caller-observable state changes; use this when resulting state is part of the contract
3. **Communication-based** — assert on collaborator interactions only when the interaction is the promised behavior; reserve this mainly for boundary behaviors

Use the lowest-brittleness style that still validates the contract.

**✅ Good**

```python
def test_price_conversion_uses_exchange_rate():
    rate_provider = StubExchangeRateProvider(rate=1.2)
    sut = PricingService(rate_provider)

    total = sut.convert_to_usd(Money.eur(10))

    assert total == Money.usd(12)
```

**⚠️ Only when interaction is the contract**

```python
def test_welcome_email_is_sent_after_registration():
    email_sender = SpyEmailSender()
    sut = RegistrationService(email_sender=email_sender)

    sut.register_user("alice@example.com")

    assert email_sender.sent_messages == [
        EmailMessage(
            to="alice@example.com",
            subject="Welcome",
        )
    ]
```

### 7. Prefer stub-family doubles over mock-family doubles

- Use **dummies** (unused placeholders), **stubs** (canned answers), and **fakes** (working simplified replacements) when collaborators only provide indirect input into the scenario.
- Use **spies** (recorded interactions) or **mocks** (interaction expectations) only when the outgoing interaction is itself part of the contract.
- If a test only needs canned data, do not also assert interactions.
- Prefer sociable tests with real collaborators where practical, but isolate when isolation clarifies the contract.

**✅ Good**

```python
def test_successful_payment_returns_success_status():
    payment_gateway = FakePaymentGateway()
    sut = PaymentProcessor(payment_gateway)

    result = sut.process_payment(100)

    assert result.status == PaymentStatus.SUCCESS
    assert result.amount == 100
```

**❌ Bad**

```python
def test_payment_processor_calls_gateway_once():
    payment_gateway = Mock()
    payment_gateway.charge.return_value = {"status": "success", "amount": 100}

    sut = PaymentProcessor(payment_gateway)
    result = sut.process_payment(100)

    payment_gateway.charge.assert_called_once_with(100)
    assert result["status"] == "success"
```

### 8. Keep integration tests few and boundary-focused

- Business logic should be unit-test dominant.
- Use integration tests for adapters, persistence, messaging, HTTP, file systems, and other external boundaries.
- Integration tests validate boundary contracts; they do not replace lower-level tests.

**✅ Good**

```python
def test_saved_user_can_be_loaded_by_email():
    repository = UserRepository(database_url="sqlite:///:memory:")
    user = User(name="Alice", email="alice@example.com")

    repository.save(user)
    loaded_user = repository.find_by_email("alice@example.com")

    assert loaded_user.name == "Alice"
    assert loaded_user.email == "alice@example.com"
```

**❌ Bad**

```python
def test_reputation_rules_through_http_and_database():
    sut = TestApiServer()

    response = sut.post("/users/reputation/recalculate", json={"user_id": 1})

    assert response.status_code == 200
    assert response.json["reputation"] == 55
```

### 9. Favor clarity over abstraction in tests

- Prefer explicit, self-contained tests over clever test abstractions.
- Apply the Rule of Three before extracting common test setup or helpers.
- Use parameterized tests only when the behavior is truly identical across cases.
- Keep important scenarios as explicit named tests when that improves diagnosis.

**✅ Good**

```python
def test_add_returns_sum_of_two_positives():
    assert add(2, 3) == 5


def test_add_returns_sum_of_two_negatives():
    assert add(-2, -3) == -5
```

**✅ Also good when the behavior is identical**

```python
@pytest.mark.parametrize(
    "left,right,expected",
    [
        (2, 3, 5),
        (-2, -3, -5),
    ],
)
def test_add(left, right, expected):
    assert add(left, right) == expected
```

### 10. Preserve signal over metrics

- Prefer explicit assertions over snapshot or golden-style approval tests.
- Treat coverage as a weak smoke signal, not a success metric.
- Treat flaky tests as determinism bugs: identify the unstable dependency and make the test repeatable.
- Use property-based testing sparingly, only when invariants are clear and the additional machinery is justified.
- Do not assert ordering unless ordering is part of the contract.

**✅ Good**

```python
def test_render_user_profile_includes_name_and_container():
    user = User(name="Alice")

    html = render_user_profile(user)

    assert "<h1>Alice</h1>" in html
    assert 'class="user-profile"' in html
```

**❌ Bad**

```python
def test_render_user_profile_matches_snapshot():
    user = User(name="Alice")

    html = render_user_profile(user)

    assert html == load_snapshot("user_profile.html")
```

## Refactoring Patterns

Use these transformations when improving tests:

### Replace implementation-coupled assertions with behavior assertions

**⬅️ Before**

```python
def test_user_service_calls_repository_once():
    repository = Mock()
    sut = UserService(repository)

    sut.get_user(user_id=1)

    repository.find_by_id.assert_called_once_with(1)
```

**➡️ After**

```python
def test_user_service_returns_user_by_id():
    repository = FakeUserRepository()
    repository.add(User(id=1, name="Alice"))
    sut = UserService(repository)

    user = sut.get_user(user_id=1)

    assert user.id == 1
    assert user.name == "Alice"
```

### Replace hidden setup with visible scenario setup

**⬅️ Before**

```python
def test_player_on_cooldown_is_not_ready():
    player = make_default_player()

    assert player.is_ready_to_attack() is False
```

**➡️ After**

```python
def test_player_on_cooldown_is_not_ready():
    player = Player(has_weapon=True, mana=10, cooldown_seconds=3)

    assert player.is_ready_to_attack() is False
```

### Replace brittle ordering assertions when order is not the contract

**⬅️ Before**

```python
def test_get_active_users():
    users = get_active_users()

    assert users == ["Alice", "Bob", "Charlie"]
```

**➡️ After**

```python
def test_get_active_users():
    users = get_active_users()

    assert set(users) == {"Alice", "Bob", "Charlie"}
```

### Replace time-based flakiness with controlled time

**⬅️ Before**

```python
def test_session_expires_after_timeout():
    sut = Session(timeout_seconds=300)

    time.sleep(301)

    assert sut.is_expired() is True
```

**➡️ After**

```python
def test_session_expires_after_timeout():
    clock = FakeClock(now=datetime(2026, 4, 5, 12, 0, 0))
    sut = Session(timeout_seconds=300, clock=clock)

    clock.advance(seconds=301)

    assert sut.is_expired() is True
```

## Testability Is a Design Signal

Code that is consistently hard to test usually has unclear boundaries, mixed responsibilities, or too much hidden state. Use testing difficulty as a signal to simplify the design.

## Quick Adherence Check

Before finishing test work, verify:

- Does the test protect public behavior rather than internals?
- Would it survive a refactor that preserves the contract?
- Is the scenario name clear and behavior-focused?
- Is the setup visible, local, and deterministic?
- Are the assertions validating outputs, observable state, or genuine contract-level interactions?
- Are doubles being used only where necessary, with stubs and fakes preferred over mocks?
- Are integration tests limited to real boundaries?

## Summary

- Test contracts, not implementation details.
- Keep tests deterministic, focused, and easy to read.
- Prefer output and state assertions over interaction-heavy tests.
- Keep setup visible; prefer stubs and fakes over mocks.
- Use integration tests sparingly at boundaries.
- Measure success by confidence in behavior, not by test volume or coverage numbers.
