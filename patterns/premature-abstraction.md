# Premature abstraction

## What it looks like

```typescript
// You asked for a function that fetches a user from the API.
// Got: a generic factory pattern.

interface DataFetcher<T> {
  fetch(id: string): Promise<T>;
}

class APIClient<T> implements DataFetcher<T> {
  constructor(private endpoint: string, private parser: (data: any) => T) {}
  async fetch(id: string): Promise<T> {
    const res = await fetch(`${this.endpoint}/${id}`);
    return this.parser(await res.json());
  }
}

const userFetcher = new APIClient<User>('/api/users', parseUser);
const getUser = (id: string) => userFetcher.fetch(id);
```

When all you needed was:

```typescript
async function getUser(id: string): Promise<User> {
  const res = await fetch(`/api/users/${id}`);
  return parseUser(await res.json());
}
```

## Why agents do this

"Robust" / "extensible" / "reusable" code in training data is over-abstract for most uses. Agents inherit that bias.

## How to detect

- **Count abstractions per concrete use.** If you have one user fetcher and three abstraction layers, you have premature abstraction.
- **Check generic types.** A generic `<T>` with one usage is suspicious.
- **Read the code with a "what happens when I call this?" mindset.** If you have to follow 4 indirections to find the actual fetch call, the abstraction is too deep.

## How to prevent

- In the prompt: "Default to concrete code. Don't add abstractions until there's a second use case. Don't add interfaces until there are two implementations."
- In CLAUDE.md: "We follow YAGNI. Concrete first, abstract when needed."
- For agents that defend abstractions: ask "what would this code look like if we only had this one use case?" Then use that.

## How to recover when you find it

- Inline the abstractions back to concrete code.
- The right time to abstract is when you have the second concrete instance and can see what's actually shared.
- Don't be precious about deleting abstractions. An abstraction that doesn't earn its complexity is a tax on every reader.
