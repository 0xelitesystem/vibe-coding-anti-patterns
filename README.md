# vibe-coding-anti-patterns

Things AI coding agents do badly, with detection strategies and fixes. Field-observed, not speculative.

## Why this exists

Most AI failures aren't dramatic. The agent doesn't refuse the task or write nonsense; it produces plausible code that's subtly wrong. The wrongness compounds as you keep building on it. By the time you notice, the codebase has 5 versions of the same utility, 3 made-up library functions, and a test suite that tests nothing.

This is a catalog of those failures and what to do about them.

## The anti-patterns

| Anti-pattern | Why it happens |
|---|---|
| [Hallucinated APIs](./patterns/hallucinated-apis.md) | Agent pattern-matches what an API should look like |
| [Made-up CLI flags](./patterns/made-up-cli-flags.md) | Agent infers flags from context that don't exist |
| [Tests that test nothing](./patterns/tests-that-test-nothing.md) | Tests pass regardless of behavior |
| [Plausible but wrong logic](./patterns/plausible-but-wrong-logic.md) | Off-by-one, wrong operator, inverted condition |
| [Silent dependency creep](./patterns/silent-dependency-creep.md) | Agent adds packages you didn't ask for |
| [Everything in one file](./patterns/everything-in-one-file.md) | Agent dumps logic without modular structure |
| [Stale knowledge of versions](./patterns/stale-knowledge-of-versions.md) | Agent uses old API patterns on new versions |
| [Wrapped-everything error handling](./patterns/wrapped-everything-error-handling.md) | Try/catch around everything, swallowing errors |
| [Confident wrong assumptions](./patterns/confident-wrong-assumptions.md) | "Fix" that's based on a wrong mental model |
| [Refactor scope creep](./patterns/refactor-scope-creep.md) | Asked for a one-line fix, got a rewrite |
| [Fake comprehensive logging](./patterns/fake-comprehensive-logging.md) | Logs everywhere that don't actually help debugging |
| [Code that compiles but doesn't run](./patterns/compiles-but-doesnt-run.md) | Type-correct nonsense |
| [Plausible config files](./patterns/plausible-config-files.md) | Config that looks right but uses wrong keys |
| [Premature abstraction](./patterns/premature-abstraction.md) | Generic interface for one implementation |

## Format

Each anti-pattern entry is structured:

```markdown
# Anti-pattern name

## What it looks like
[Concrete example]

## Why agents do this
[Mechanistic explanation, not "AI is bad"]

## How to detect
[Specific checks, not "review carefully"]

## How to prevent
[Prompt/workflow changes]

## How to recover when you find it
[What to do after the fact]
```

## How to use this catalog

- **Reading once** before starting a project, calibrates your suspicion
- **Reference** when you spot something off, name the pattern, find the entry, follow detection
- **Onboarding doc** for teams new to AI coding tools

## Contribute

PRs welcome. See [CONTRIBUTING.md](./CONTRIBUTING.md).

## Related

- [ai-coding-prompt-recipes](https://github.com/0xelitesystem/ai-coding-prompt-recipes) - prompts that prevent some of these
- [ai-generated-code-review-rubrics](https://github.com/0xelitesystem/ai-generated-code-review-rubrics) - rubrics that catch them
- [vibe-coding-test-strategies](https://github.com/0xelitesystem/vibe-coding-test-strategies) - how to test AI code

## License

MIT.
