# Everything in one file

## What it looks like

You asked for a feature with three components (controller, service, model). The agent put them all in `feature.ts` as 800 lines.

Or: you asked for a CLI tool. It's all in `main.go` including arg parsing, business logic, and I/O.

## Why agents do this

The agent's context is the conversation. It can write more efficiently if everything is in one place. It defers structural decisions because "we can refactor later."

## How to detect

- **Watch file sizes.** A new file >300 lines on first commit is suspicious.
- **Watch for missing structure.** No `services/`, no `models/`, no clear layering, the agent skipped it.
- **Watch for "let's refactor later" comments.** Sometimes literal, sometimes implied by giant if/else chains where polymorphism would fit.

## How to prevent

- In CLAUDE.md, document your project's structure: "Controllers go in src/controllers/. Services in src/services/. Models in src/models/. Pure utilities in src/lib/. New code follows this structure."
- In the prompt for new features: "The feature should be structured across at least these files: [LIST]. Don't put everything in one file."
- After scoping, before code: ask the agent which files will change and verify the structure is sensible.

## How to recover when you find it

- Splitting after-the-fact is harder than getting it right initially. But it's worth doing because the next agent session will compound the mess.
- Use the `extract-shared-utility` recipe-style approach: extract one component at a time, with tests still passing.
- Update CLAUDE.md with the structure rules so this doesn't happen again.
