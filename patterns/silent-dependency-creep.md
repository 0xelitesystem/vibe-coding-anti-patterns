# Silent dependency creep

## What it looks like

```bash
$ git diff package.json
+ "lodash": "^4.17.21",
+ "moment": "^2.30.0",
+ "axios": "^1.6.0",
```

You asked for a small bug fix. The agent added three dependencies you didn't need (you already have native equivalents).

## Why agents do this

Training data is full of code that imports popular libraries. The agent reaches for `lodash.cloneDeep` because that's what it's seen, not `structuredClone` (which is native and equivalent for most cases). It doesn't know your project's "no new deps" policy unless you tell it.

## How to detect

- **Always check `package.json` / `requirements.txt` / `go.mod` for changes after agent work.**
- **Diff the lockfile too.** A new top-level dep can pull in dozens of transitive ones.
- **Watch for famously-replaceable libraries:** lodash (most uses are now native), moment (replaced by Temporal/date-fns), uuid (now native via crypto.randomUUID), axios (native fetch works).

## How to prevent

- In CLAUDE.md / .cursorrules: "Do not add new dependencies without asking. List of approved libraries: [...]. If you need something else, ask."
- In the prompt: "No new dependencies. Use stdlib / what's already in package.json."
- Lock the lockfile: review every change to it, ideally in a separate commit.

## How to recover when you find it

- Remove the unwanted dep.
- Replace its usage with native equivalents or what was already in the project.
- Ask: did this happen because the project's existing utilities aren't discoverable? If so, document them in CLAUDE.md so future agents see them.
