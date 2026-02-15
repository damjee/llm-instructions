# Feedback: coding/godot-gdscript-standards.md

## What Works Well

- **Layered architecture** — "Apply these standards on top of Clean Code Standards" is a smart pattern. It avoids repetition and tells the LLM exactly how to compose multiple instruction docs. This is best-practice for multi-document prompt design.
- **Exhaustive code examples** — Nearly every rule has a ✅/❌ pair. This is the most effective way to instruct an LLM. The verb prefix table with `calculate_`, `update_`, `check_`, etc. is especially actionable.
- **Complete example at the end** — The full Player class example is gold. LLMs perform dramatically better when they have a complete "reference implementation" to pattern-match against. This single example probably does more work than half the rules above it.
- **Code order section** — The numbered ordering (annotations → class → signals → enums → constants → exports → vars → @onready → methods) is exactly the kind of deterministic, unambiguous instruction LLMs need.
- **"User Preferences" framing** — Separating universal GDScript conventions from your personal preferences (verb names, static typing, no Hungarian notation) is clever. It signals to the LLM which rules are negotiable vs. non-negotiable.

## Suggestions for Improvement

### 1. ~~The Complete Example Has Inconsistencies with the Rules~~ [FIXED]

~~The example has some mismatches that would confuse an LLM~~

**This issue has been resolved.** All private method calls now correctly use underscore prefixes (`_initialize_player()`, `_update_movement()`, `_die()`, etc.) to match their definitions.

### 2. The Formatting Section's "Good" Example Uses Tabs but Renders as No Indentation

In the "Indentation and Whitespace" section, the "GOOD" code block appears to have no indentation at all (lines like `var result: int = int(base * multiplier)` appear flush-left). This is likely a markdown rendering issue with tabs, but it means the LLM sees the "good" example as having no indentation — the opposite of the rule. Consider using a comment like `# (indented with tabs)` or ensuring the markdown renders correctly.

### 3. @export vs @onready Guidance Could Be Clearer

The doc says "Prefer @export over @onready for external nodes" but the complete example uses `@onready` for `animation_player` and `collision_shape`. The rule about when each is appropriate (external = @export, same-scene children = @onready) could be stated more concisely as a simple decision rule:
- "Use `@export` for nodes wired from other scenes. Use `@onready` only for direct children within the same scene."

### 4. Missing: Common GDScript Patterns

The doc covers anti-patterns well but could benefit from a few positive patterns unique to Godot:
- **State machine pattern** — Since you have a `State` enum, showing a minimal state machine structure would be valuable.
- **Resource pattern** — When to use custom Resources vs. nodes.
- **Autoload/singleton guidance** — When it's appropriate vs. dependency injection.

These are frequent decision points where LLMs need guidance in Godot projects.

### 5. Signal Naming: Only Past Tense?

The doc says signals should be past tense (`health_changed`, `player_died`). This works for "event happened" signals, but what about request signals like `damage_requested` or `spawn_requested`? A note acknowledging that past tense applies to event notifications while request signals may use different patterns would make this more complete.

### 6. No Guidance on `match` Statement Style

The complete example uses `match` for animation state, but there are no rules about `match` formatting, when to prefer `match` over `if/elif`, or how to handle fallthrough. Since `match` is a frequent GDScript construct, a brief section would be useful.

### 7. Consider Adding a "File Template"

Since the code order section is so detailed, turning it into a copy-paste file template (a skeleton `.gd` file with placeholder comments) would be extremely useful. LLMs could use it as a starting point when creating new files.

## Overall Rating

**Excellent domain-specific instruction doc.** The layered approach, exhaustive examples, and clear ordering rules make this highly effective for LLM consumption. ~~The #1 priority fix is the method name inconsistencies in the complete example — that will directly cause bugs in LLM-generated code.~~ [FIXED] The main remaining improvements are around edge cases (signal naming for non-event types, `match` statement style) and optional additions (state machine patterns, file template).
