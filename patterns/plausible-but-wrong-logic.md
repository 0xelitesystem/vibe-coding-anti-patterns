# Plausible but wrong logic

## What it looks like

```python
# Off-by-one
def get_last_three(items):
    return items[-3:-1]  # actually returns last 3 minus the last one (so 2 items)
```

```typescript
// Inverted condition
if (!user.isActive || user.isAdmin) {
  // Allows admins through, blocks active users, backwards
  allowAccess();
}
```

```go
// Wrong comparison
if len(items) > 0 && items[len(items)] == target {
  // Index out of bounds; should be len(items)-1
}
```

## Why agents do this

Code that's the right SHAPE but the wrong DETAIL is the most common AI bug. The agent generates code that looks like the kind of code that would solve the problem; the specific operator/index/condition gets fuzzed.

## How to detect

- **Tests with edge cases.** Empty inputs, single-element inputs, boundary values, off-by-one positions.
- **Type-checker doesn't catch this.** Most plausible-wrong code is type-correct.
- **Property-based testing.** If you generate inputs and check invariants, this class of bug shows up.
- **Manual review with adversarial mindset.** "What's the input that breaks this?"

## How to prevent

- Specify edge cases in the prompt: "Code must handle: empty input, single-element, exactly N elements, more than N elements."
- Ask the agent to enumerate edge cases BEFORE writing code: "What are the edge cases for [function]? List them, then write the function, then write a test for each."
- Use stricter types where they help. Index types in Go via array sizes. NewType wrappers in Python. Branded types in TypeScript.

## How to recover when you find it

- Once you find one off-by-one, look for siblings. Agents that get one boundary wrong tend to get others wrong too.
- Add edge case tests for everything in the same module, not just the bug you found.
- Don't just patch the specific bug; ask whether the abstraction is right. If you're constantly off-by-one, maybe ranges should be expressed differently.
