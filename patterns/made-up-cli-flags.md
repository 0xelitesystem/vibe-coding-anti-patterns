# Made-up CLI flags

## What it looks like

```bash
# Agent's "solution":
ffmpeg -i input.mp4 --auto-deinterlace --quality high output.mp4

# Reality: ffmpeg doesn't have --auto-deinterlace or --quality flags.
```

```bash
# Agent uses git in a way git doesn't support:
git rebase --strategy=preserve-merges main  # this option doesn't exist
```

## Why agents do this

CLI flags are dense, version-specific, and similar tools have different conventions (`--quality`, `-q`, `-quality`, all with subtle differences). Agents pattern-match what a flag name SHOULD be rather than what it IS in a specific tool version.

## How to detect

- **Run the command.** Most tools error on unknown flags.
- **Check the actual `--help`.** `ffmpeg -h | grep deinterlace` if the agent suggests `--auto-deinterlace`.
- **Check the version.** Many flags are version-gated. The agent might have seen a flag from a different version.
- **Cross-reference with the man page or official docs.** Especially for `git`, `docker`, `kubectl`, and other tools with sprawling flag spaces.

## How to prevent

- In the prompt, specify the exact tool version you have. "Using ffmpeg 6.1, ..."
- For one-off CLI tasks, paste the relevant section of `--help` output as ground truth.
- For frequent tools, document the flags you actually use in CLAUDE.md so the agent has a known-good reference.

## How to recover when you find it

- Don't try to "fix" by renaming the flag. The agent picked a flag that fit a model of how the tool works; that model may be wrong.
- Read what the tool actually offers and figure out what command achieves your goal.
- If the goal isn't achievable with the tool you specified, that's a real signal, pick a different tool or break the task differently.
