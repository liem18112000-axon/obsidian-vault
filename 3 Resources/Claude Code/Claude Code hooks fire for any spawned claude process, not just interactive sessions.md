---
title: "Claude Code hooks fire for any spawned claude process, not just interactive sessions"
created: 2026-07-11
type: concept
status: seedling
source: "Vinnstack BDD implement-stage debugging, 2026-07-11"
tags: [claude-code, hooks, architecture]
---

# Claude Code hooks fire for any spawned claude process, not just interactive sessions

Claude Codes hook system (`PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`, etc., configured in `settings.json`) is a property of the **user/project/local settings scope being loaded**, not of "being an interactive session." Any process that spawns a `claude`/`claude -p` child — a script, a web app backend, an orchestration tool — picks up the same `~/.claude/settings.json` ("user" scope) hooks a person's own foreground Claude Code sessions have, unless that scope is explicitly excluded.

This matters because personal automation hooks (approval gates, Telegram notifications, reminders that inject a message after a tool call) are typically authored assuming a human is present to react to them. A headless/automated caller has no one to respond, so the spawned model either stalls waiting on something that will never resolve the way an interactive turn would, or — more commonly — just narrates/reacts to the injected hook output as prose instead of completing whatever strict machine-readable contract the caller expected.

Practical implication: any codebase that programmatically spawns `claude -p` for structured/parseable output should default to excluding the "user" settings scope (`--setting-sources project,local`) rather than assuming hooks are opt-in or interactive-only. See [[Headless claude -p inherits personal ~/.claude/settings.json hooks]] for the specific incident and the caveat about `--bare`.
