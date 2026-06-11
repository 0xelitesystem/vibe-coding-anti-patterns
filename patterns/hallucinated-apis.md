# Hallucinated APIs

## What it looks like

```python
# Agent's "fix": uses pandas.DataFrame.smart_merge()
# Reality: smart_merge is not a method on DataFrame.
df.smart_merge(other, on='id', strategy='best')
```

```typescript
// Agent calls a method that doesn't exist on the library
import { z } from 'zod';
const schema = z.object({...}).strictMode(); // strictMode is not a thing
```

```go
// Agent imports a stdlib function that isn't there
import "strings"
result := strings.ReplaceFirst(s, "old", "new") // doesn't exist
```

## Why agents do this

Pattern-matching. The agent has seen many APIs in training data. When it generates code for a library, it's predicting what method names would EXIST given the rest of the API surface, not retrieving documented methods. The longer the API has been around, the better; the newer or smaller the library, the worse.

## How to detect

- **Run the code.** Compilation/import errors catch most hallucinated stdlib calls. Tests catch the rest.
- **Grep the dependency's source for the method name.** If it's not there, the agent invented it.
- **Check imports.** Agent imports `strings.ReplaceFirst` from `strings`, search the actual `strings` package for `ReplaceFirst`.
- **Search official docs for the exact method name.** Suspiciously easy-to-find methods that aren't in docs are hallucinations.

## How to prevent

- In your prompt, point the agent at the actual documentation: "Use only methods documented at [URL]. If a method you want isn't there, ask before using."
- For less-common libraries, paste a snippet of the library's actual API surface into the prompt as ground truth.
- Use a `.cursorrules` / `CLAUDE.md` that lists allowed methods for any library where hallucination has bitten you.

## How to recover when you find it

- Don't just rename to a real method that "looks similar." The agent picked the wrong API for a reason; the right code may need a different shape entirely.
- Read the real docs. Find the actual method that does what you need.
- If no such method exists, the task may need different code, and that's a real signal.
