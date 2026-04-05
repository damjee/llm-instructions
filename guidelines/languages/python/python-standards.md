# Python Standards

**Purpose:** Python-specific coding conventions and style guidance  
**Audience:** AI agents working with Python codebases  
**Scope:** Python language practices, structure, and readability conventions  
**When to Load:** Load when the repository or task uses Python.  
**Load Order:** Load after Clean Code Standards; also load Testing Philosophy if tests or testability are in scope.  
**Skip If:** The task is language-agnostic or the project is not Python.  
**Typical Tasks:** Implement Python features, refactor Python modules, review Python code, improve type usage, modernize Python style.

---

## Core Requirements

Apply these standards on top of [Clean Code Standards](../../clean-code-standards.md). This document adds **Python-specific** conventions and starter preferences for this repository.

---

## Starter Preferences

### Prefer explicit, descriptive Python

- Favor obvious control flow over compact cleverness.
- Use Python features that improve readability, not density.
- Prefer small functions and named intermediate values when expressions become hard to scan.

**✅ Good**

```python
def calculate_total_price(items: list[CartItem]) -> Decimal:
    subtotal = sum(item.price for item in items)
    tax = subtotal * TAX_RATE
    return subtotal + tax
```

**❌ Bad**

```python
def total(items):
    return sum(i.price for i in items) * 1.08
```

### Use type hints at module boundaries

- Add type hints to public functions, methods, and important variables.
- Prefer concrete, readable types over untyped containers.
- Keep type information in annotations, not variable names.

**✅ Good**

```python
def find_user_by_email(email: str) -> User | None:
    return user_repository.find_by_email(email)


active_users: list[User] = []
```

**❌ Bad**

```python
def find_user_by_email(str_email):
    return user_repository.find_by_email(str_email)


user_list = []
```

### Prefer `pathlib`, dataclasses, and standard library tools when they fit

- Prefer `Path` over stringly-typed file paths.
- Prefer `@dataclass` for simple structured data with little behavior.
- Reuse standard library facilities before adding custom helpers.

**✅ Good**

```python
from dataclasses import dataclass
from pathlib import Path


@dataclass(slots=True)
class ExportRequest:
    source_path: Path
    output_path: Path
```

**❌ Bad**

```python
class ExportRequest:
    def __init__(self, src, dst):
        self.src = src
        self.dst = dst
```

### Prefer `snake_case` for functions and variables, `PascalCase` for classes

- Follow standard Python naming conventions consistently.
- Name booleans as predicates such as `is_ready`, `has_access`, or `can_retry`.
- Name constants in `UPPER_SNAKE_CASE`.

**✅ Good**

```python
MAX_RETRY_COUNT = 3


class PaymentProcessor:
    def can_retry_payment(self) -> bool:
        return self.retry_count < MAX_RETRY_COUNT
```

**❌ Bad**

```python
maxRetryCount = 3


class payment_processor:
    def Retry(self):
        return True
```

### Prefer `async` / `await` over callback-style async flow

- Keep asynchronous control flow linear and readable.
- Await work directly instead of manually chaining callbacks or background tasks unless concurrency is required.
- Use `asyncio.gather` or task groups only when parallel work is intentional.

**✅ Good**

```python
async def load_dashboard(user_id: str) -> Dashboard:
    profile = await profile_service.load(user_id)
    notifications = await notification_service.load_for_user(user_id)
    return Dashboard(profile=profile, notifications=notifications)
```

**❌ Bad**

```python
async def load_dashboard(user_id):
    profile_task = asyncio.create_task(profile_service.load(user_id))
    notifications_task = asyncio.create_task(notification_service.load_for_user(user_id))
    return Dashboard(
        profile=await profile_task,
        notifications=await notifications_task,
    )
```

### Avoid mutable default arguments

- Default optional collections to `None`, then create a new value inside the function.
- Treat mutable defaults as shared state bugs unless the shared behavior is deliberate.

**✅ Good**

```python
def add_tag(tag: str, tags: list[str] | None = None) -> list[str]:
    current_tags = [] if tags is None else list(tags)
    current_tags.append(tag)
    return current_tags
```

**❌ Bad**

```python
def add_tag(tag, tags=[]):
    tags.append(tag)
    return tags
```

## Summary

- Prefer readable Python over dense Python.
- Use type hints at public boundaries.
- Follow standard Python naming conventions.
- Prefer `pathlib`, dataclasses, and standard library tools when they fit.
- Keep async flow explicit and simple.
- Avoid mutable default arguments.
