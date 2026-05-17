# Refactor Candidates

After TDD cycle, look for:

- **Duplication** → Extract function/class
- **Long methods** → Break into private helpers (keep tests on public interface)
- **Shallow modules** → Combine or deepen
- **Feature envy** → Move logic to where data lives
- **Primitive obsession** → Introduce value objects
- **Mechanics-heavy test names** → Rename to scenarios and behaviors
- **Hidden test setup** → Make the scenario visible in the test
- **Existing code** the new code reveals as problematic
