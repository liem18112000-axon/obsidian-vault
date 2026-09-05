---
title: "pip install OOM-killed (exit 137) under the sandbox; retry with sandbox disabled"
created: 2026-08-20
type: lesson
status: seedling
source: "session 2026-08-20, customer360-api"
tags: [claude-code, sandbox, oom, exit-137, gotcha]
---

# pip install OOM-killed (exit 137) under the sandbox; retry with sandbox disabled

When a background agent runs `pip install` inside the Claude Code sandbox, a large/compiling dependency set can be **OOM-killed** — the process dies with **exit code 137** (128 + SIGKILL 9), not a normal pip error. This looks like a dependency-resolution failure but is really the sandbox memory cap.

**Fix:** re-run the same `pip install` with the sandbox disabled (the memory ceiling is what kills it, not the packages). Observed installing the `customer360-api` requirements; the retry succeeded unchanged.

General rule: an **exit 137** from any build/install step ≈ out-of-memory (killed), so raise the memory limit or drop the sandbox rather than editing the command.
