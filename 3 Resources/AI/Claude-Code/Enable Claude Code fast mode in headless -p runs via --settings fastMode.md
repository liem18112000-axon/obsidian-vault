---
title: "Enable Claude Code fast mode in headless -p runs via --settings fastMode"
created: 2026-07-28
type: howto
status: seedling
source: "claude-code-guide agent, session 2026-07-28"
tags: [claude-code, fast-mode, headless, cli, vinnstack]
---

# Enable Claude Code fast mode in headless -p runs via --settings fastMode

Claude Code "Fast mode" (Opus with faster OUTPUT — not a smaller model; toggled interactively with `/fast`) CAN be enabled for HEADLESS `claude -p` runs — but only via the `--settings` flag, not a dedicated flag or env var.

- CLI flag: there is NO `--fast` flag. Enable it with `--settings {"fastMode": true}`.
- Env var: NONE (no `CLAUDE_FAST_MODE` / `ANTHROPIC_FAST_MODE`). `--settings` is the only lever.
- In non-interactive `-p` mode, `/fast` only works if the session was launched with `--settings {"fastMode": true}` already; you cannot toggle it on mid-run.
- Prerequisites are account/subscription-level: an active Claude Code subscription (Pro/Max/Team/Enterprise) or Console API access, usage credits enabled, and (Team/Enterprise) fast mode enabled at the org level. Then you opt in per headless session.

Example: `claude -p --model claude-opus-4-8 --settings {"fastMode": true} --permission-mode dontAsk --allowedTools ... --append-system-prompt "..."`.

For an app that spawns the bundled @anthropic-ai/claude-code binary headlessly (e.g. Vinnstack, in lib/ai/claudeRun.ts), add `--settings {"fastMode": true}` to the spawn args — ideally gated behind a config toggle. Docs: code.claude.com/docs/en/fast-mode.md and headless.md.

Related: [[3 Resources/Languages/Node.js/Config read into a module-level const applies only on next process launch]] (same project, Vinnstack, drives Claude via headless `claude -p`).

## Related

- [[3 Resources/Languages/Node.js/Config read into a module-level const applies only on next process launch]]
