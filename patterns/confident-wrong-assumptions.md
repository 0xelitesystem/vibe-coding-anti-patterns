# Confident wrong assumptions

## What it looks like

You: "Why is this query slow?"

Agent: "The issue is missing indexes on the user_id column. I've added a migration."

Reality: The user_id column already has an index. The slowness is from a JOIN that returns 100k rows because of a missing WHERE clause. The agent didn't check.

## Why agents do this

Confidence is a default. The agent generates an answer with the same confidence regardless of whether it actually verified. It pattern-matches "slow query -> probably missing index" without checking.

## How to detect

- **Verify each claim.** "The user_id column needs an index" -> check `\d users` or `SHOW INDEXES FROM users`. Most diagnostic claims can be verified with a single command.
- **Watch for unbacked claims.** "This is happening because X" without showing X.
- **Ask "how do you know?"** A real diagnosis cites evidence (log line, query plan, profiler output). A guessed diagnosis cites general knowledge.

## How to prevent

- In the prompt: "Before proposing a fix, gather evidence. Show me the relevant log line, query plan, profiler output, or test that demonstrates the problem. Don't guess."
- Provide the evidence yourself when possible: "Here's the slow query log entry. Here's the query plan."
- Ask the agent to enumerate possible causes BEFORE picking one: "What are the 3-5 most likely causes? Which can you verify from the data I gave you?"

## How to recover when you find it

- Roll back the "fix" if it didn't address the actual cause.
- Re-investigate from the evidence, not the agent's narrative.
- Note the failure: "agent assumed indexing problem; actual cause was the WHERE clause." Add this kind of failure to your team's calibration of when to trust diagnostic suggestions.
