# Plausible config files

## What it looks like

```yaml
# Agent generates a Kubernetes manifest with reasonable-looking but wrong fields
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:latest
          resources:
            limits:
              cpu: 500m
              ram: 512Mi  # WRONG - should be 'memory: 512Mi', not 'ram'
```

```toml
# Wrong config key for the actual tool
[tool.ruff]
line-length = 100
ignore = ["E501"]  # 'ignore' was renamed to 'lint.ignore' in newer ruff
```

## Why agents do this

Config files have a lot of structure. Agents pattern-match the structure (YAML keys, TOML sections) but specific keys vary by version and aren't easy to verify without docs.

## How to detect

- **Run the validator.** `kubectl apply --dry-run=client`, `helm lint`, `terraform validate`, `ruff check --show-files`.
- **Check actual docs for the version of the tool.** Especially for tools with major migrations (ruff, eslint flat config, etc.).
- **Apply the config in a test environment.** Many config errors only surface at apply time.

## How to prevent

- Specify the version of the tool in the prompt or CLAUDE.md: "ruff version 0.6.x. eslint flat config (v9). Helm v3."
- For non-trivial config, paste a working example from the same project as ground truth.
- Use schema validation where available (JSON Schema for k8s, taplo for TOML).

## How to recover when you find it

- Run the validator first. Fix what it reports.
- For tools without strict validation, deploy to a test environment before production.
- Document the corrected version in CLAUDE.md so it doesn't recur.
