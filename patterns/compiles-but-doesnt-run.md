# Code that compiles but doesn't run

## What it looks like

```typescript
// Type-correct nonsense
const handler = async (req: Request): Promise<Response> => {
  const data = await req.json() as User;
  // data could be anything, but TypeScript thinks it's User now
  await db.users.create(data);  // throws at runtime if data isn't shaped right
  return new Response();
};
```

```python
# Annotation says one thing, behavior says another
def get_user(id: int) -> User:
    return None  # User type promised, None returned
```

## Why agents do this

Type systems get pattern-matched. Agents are good at making types align in a syntactic sense but can miss semantic mismatches. Casts and `as` assertions are particularly dangerous.

## How to detect

- **Run the code with realistic inputs, not just unit tests.**
- **Look for `as` (TypeScript), type assertions, `# type: ignore` (Python), `interface{}` casts (Go).** Each one is a place where the type system was bypassed.
- **Validate types at runtime where they cross trust boundaries.** Schema validators (zod, pydantic, ajv) catch what the static type system misses.

## How to prevent

- In CLAUDE.md: "Do not use `as` casts. Use schema validation (zod) for any data coming from the network or filesystem. Don't suppress type errors with `// @ts-ignore` or `# type: ignore`."
- In the prompt: "Type-correct code that crashes at runtime is a failure. Validate runtime data against schemas where it enters the system."
- Use stricter compiler flags: `strict: true` in TS, `--strict` in Python (mypy), no `interface{}` in Go without justification.

## How to recover when you find it

- Replace `as` casts with proper validation.
- For data crossing boundaries, add schema validation.
- For internal type assertions, ask: why did we need this cast? Often the answer is "the original type was wrong", fix that instead.
