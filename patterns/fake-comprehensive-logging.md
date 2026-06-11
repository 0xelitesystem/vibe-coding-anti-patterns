# Fake comprehensive logging

## What it looks like

```typescript
async function processOrder(order: Order) {
  console.log('processOrder called');
  console.log('order:', order);
  console.log('about to validate');
  validate(order);
  console.log('validation done');
  console.log('about to charge');
  await charge(order);
  console.log('charge done');
  console.log('about to ship');
  await ship(order);
  console.log('ship done');
  console.log('processOrder complete');
}
```

Looks comprehensive. Tells you nothing useful when something goes wrong because:
- All at the same level (no severity)
- No correlation IDs
- Logs the entire order object (PII risk)
- "About to X / X done" doesn't help when X is what's failing
- No error logging where it would matter

## Why agents do this

The pattern of "log around every operation" looks defensive. The agent confuses "lots of logs" with "useful logs."

## How to detect

- Every function has 3-5 console.log/print statements at entry/exit
- No log levels (everything is `console.log` or `print`)
- Logs include full objects (potentially PII)
- Errors are logged but execution continues anyway
- No correlation/request IDs

## How to prevent

- In CLAUDE.md: "Log levels: error for failures, warn for recoverable issues, info for state changes a sysadmin would care about, debug for development. No log statements at function entry/exit. Never log full request/response bodies."
- In the prompt: "Don't add logging unless I asked for it. If diagnostics are needed, propose specific log statements with rationale."
- Use a structured logger (pino, structlog, slog) with a real log level configuration.

## How to recover when you find it

- Strip the noise. Keep logs at boundaries (request received, response sent, external call started/finished, error occurred).
- Add correlation IDs at the boundary so logs can be threaded together.
- Audit for PII in log calls. Replace with redacted versions or omit.
- Establish log levels and stick to them.
