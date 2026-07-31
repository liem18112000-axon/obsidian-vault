---
title: "Claude Code hooks fire for any spawned claude process, not just interactive sessions"
created: 2026-07-11
type: lesson
status: seedling
source: "Vinnstack BDD implement-stage debugging, 2026-07-11"
tags: [claude-code, hooks, headless, architecture, gotcha, vinnstack]
aliases:
  - Headless claude -p inherits personal ~/.claude/settings.json hooks
---

# Claude Code hooks fire for any spawned claude process, not just interactive sessions

Claude Code's hook system (`PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`, … in `settings.json`) is a property of the **settings scope being loaded**, not of "being an interactive session." Any process that spawns a `claude -p` child - a script, a web backend, an orchestrator - loads the operator's personal `~/.claude/settings.json` ("user" scope) hooks too, unless that scope is explicitly excluded.

Personal automation hooks (approval gates, Telegram notifications, Obsidian capture reminders) assume a human is present to react. A headless caller has nobody to answer, so the model cooperatively responds to the injected hook text as prose ("here's what I found, let me know how to proceed") instead of emitting the caller's required contract - the run looks hung or returns unusable output even though the task itself was fine.

**Fix:** spawn with `--setting-sources project,local`. This drops the "user" scope while keeping the repo's own `.claude/settings.json` / `settings.local.json`. Make it the default for any codebase that spawns `claude -p` for parseable output.

**Do NOT reach for `--bare`** as the "skip hooks" flag - it also forces Anthropic auth to strictly `ANTHROPIC_API_KEY` / `apiKeyHelper`, skipping OAuth and keychain reads, so an app relying on the operator's interactive login fails to authenticate outright ([[--bare forces API-key-only auth in Claude Code]]).

Incident: `lib/ai/claudeRun.ts`'s `runClaude()` in Vinnstack kept dying with "Implementation produced no usable result" - the user's `simplify-gate.ps1` / `reusable-gate.ps1` hooks (PostToolUse on `Edit|Write|MultiEdit` in `~/.claude/settings.json`) intercepted the run's Write calls and the model answered the gate instead of emitting its JSON contract.

## Related

- [[--bare forces API-key-only auth in Claude Code]]
- [[A globally-bootstrapped MCP server loads into every headless claude spawn]]
