# Refactor scope creep

## What it looks like

You asked: "rename `getUserData` to `loadUser`"

You got: 23 files changed. The agent renamed the function, then "while it was there" reorganized the auth module, switched fetch -> axios, added comprehensive logging, and moved a few constants to a new file.

## Why agents do this

The agent sees opportunities and assumes you'll appreciate the bonus work. The training signal didn't strongly distinguish "what the user asked for" from "what would also be good."

## How to detect

- **Diff line count vs ask.** A one-line rename should be ~5-20 lines of diff (the rename + import updates). 200 lines of diff means scope creep.
- **Files changed vs ask.** A change in `userService.ts` shouldn't touch `authMiddleware.ts` for a rename.
- **Read the diff for changes that aren't the asked thing.** "Why is this constant moved?"

## How to prevent

- Be explicit in the prompt: "ONLY rename `getUserData` to `loadUser`. Do not change anything else. If you find unrelated issues, list them as follow-ups."
- For larger work: use the `add-feature-incrementally` recipe to force step-by-step.
- Code review with: "is every change in this diff necessary for the stated goal?"

## How to recover when you find it

- Reset the changes and try again with explicit constraints.
- If the unrelated changes are good, accept them but in SEPARATE commits, don't let one commit do five things.
- Update CLAUDE.md: "Default to minimum diff. Unrelated improvements go in separate commits/PRs."
