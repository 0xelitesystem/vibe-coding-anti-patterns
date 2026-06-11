# Contributing an Anti-Pattern

## What makes a good anti-pattern entry

A good entry describes a SPECIFIC failure mode you've actually seen, with a way to detect and prevent it. Not "AI is bad at X."

Every entry must include:

1. **What it looks like** - a concrete code example
2. **Why agents do this** - mechanistic explanation
3. **How to detect** - specific checks
4. **How to prevent** - prompt/workflow changes
5. **How to recover** - what to do after finding it

## Submission

1. Add a new file in `patterns/`
2. Use existing entries as templates
3. Add a row to README.md table
4. Open a PR

PR title: `add pattern: name-of-pattern`

## What gets rejected

- Generic complaints ("AI hallucinates")
- Patterns without concrete code examples
- Patterns where "how to detect" is "review carefully"
- Patterns that are really about a single tool's bug
- Patterns that promise blanket solutions ("turn this off and you're safe")

## What gets accepted gladly

- Stack-specific patterns (Rust borrow-checker workarounds gone wrong, etc.)
- Patterns specific to particular agent tools
- Patterns that surface in CI/CD or production rather than local dev
- Patterns in non-code outputs (docs, configs, infra)
- Translations to other languages

## License

By contributing, you agree your contribution is MIT-licensed.
