# Stale knowledge of versions

## What it looks like

```typescript
// Agent uses pre-React-18 patterns in a React 19 project
import { render } from 'react-dom';  // deprecated
render(<App />, document.getElementById('root'));

// Should be (React 18+):
import { createRoot } from 'react-dom/client';
const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

```python
# Agent uses old Pydantic v1 syntax in a v2 project
class User(BaseModel):
    class Config:           # v1 pattern
        orm_mode = True
# v2 uses model_config = ConfigDict(from_attributes=True)
```

## Why agents do this

Training data has more examples of older versions than newer ones. The agent learned the older pattern more confidently. When asked to write code, it defaults to what it learned best.

## How to detect

- **Pin versions in your prompt.** "React 19. Next.js 15. Tailwind 4. Pydantic 2." Then watch for code that doesn't match those versions.
- **Run linters.** Many libraries ship migration codemods that flag deprecated patterns.
- **Check release notes.** When a major version came out, what changed? Code using the old shape is using stale knowledge.

## How to prevent

- Always specify the major version of every library in CLAUDE.md or in the prompt.
- For libraries with major rewrites (React, Vue, Pydantic, Next.js, Tailwind, etc.), explicitly say "use [VERSION] patterns."
- Provide a sample of correct code from your codebase as ground truth.

## How to recover when you find it

- Don't just patch the one occurrence. Grep the codebase for the deprecated pattern; agents tend to use the same stale pattern repeatedly.
- Add the correction to CLAUDE.md so it doesn't recur.
- For large-scale stale-pattern usage, look for codemods (e.g. React's, Vue's, Next's official migration tools) before manual fixes.
