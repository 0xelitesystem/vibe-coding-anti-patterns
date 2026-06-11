# Wrapped-everything error handling

## What it looks like

```typescript
async function getUser(id: string) {
  try {
    try {
      const user = await db.users.find(id);
      try {
        return JSON.parse(user.metadata);
      } catch (e) {
        console.error('parse error', e);
        return null;
      }
    } catch (e) {
      console.error('db error', e);
      return null;
    }
  } catch (e) {
    console.error('outer error', e);
    return null;
  }
}
```

Or: every function returns `{success: true, data: ...} | {success: false, error: ...}` even when the function never has a meaningful failure mode.

## Why agents do this

"Robust" code in training data often has try/catch. The agent applies the pattern uniformly without thinking about whether each layer can actually fail or whether failures should propagate.

## How to detect

- **Count try/catch blocks per file.** If most functions have one, you have this anti-pattern.
- **Check the catches.** Are they doing anything useful, or just `console.error` and returning null?
- **Trace error propagation.** Can the caller distinguish "user not found" from "database is down"? If everything returns null, no.

## How to prevent

- In CLAUDE.md: "Errors propagate by default. Only catch when you can do something specific (retry, fallback, transform). Don't catch to log; log at the boundary."
- In the prompt: "Don't add try/catch unless this layer can actually recover. Let errors propagate to the boundary."
- Code review specifically for this pattern.

## How to recover when you find it

- Pull out unnecessary try/catch.
- Centralize error handling at boundaries: HTTP request handlers, CLI command handlers, queue worker top-level. NOT in every utility function.
- Replace generic `catch (e) { return null }` with explicit error types where recovery is meaningful.
- The "right" amount of error handling is much less than agents default to.
