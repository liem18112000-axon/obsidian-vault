---
title: "Headless claude -p inherits personal ~/.claude/settings.json hooks"
created: 2026-07-11
type: lesson
status: seedling
source: "Vinnstack BDD implement-stage debugging, 2026-07-11"
tags: [claude-code, headless, hooks, gotcha, vinnstack]
---

# Headless claude -p inherits personal ~/.claude/settings.json hooks

A `claude -p` subprocess spawned from application code still loads the operators personal `~/.claude/settings.json` ("user" scope) by default — including PostToolUse hooks meant for interactive foreground sessions (review "gates", Telegram-approval waits, Obsidian reminders, etc.).

When the app grants the headless run Write/Edit tools (e.g. to let it author files) and one of these hooks fires after a tool call, its injected message has no human on the other end to respond to it. The model, being cooperative, reacts to the hook's prompt with prose ("here's what I found, let me know how to proceed") instead of finishing the caller's required output contract (e.g. a strict JSON envelope) — so the run appears to "hang" or return unusable garbage, even though nothing is actually broken about the task itself.

**Fix:** pass `--setting-sources project,local` to the spawned `claude` CLI. This excludes the "user" scope (where personal hooks/gates live) while keeping the repo's own `.claude/settings.json`/`settings.local.json`.

**Do NOT reach for `--bare` instead** — it looks like the obvious "skip hooks" flag, but it also forces Anthropic auth to be strictly `ANTHROPIC_API_KEY` / `apiKeyHelper`, skipping OAuth and keychain reads entirely. Any app that deliberately spawns headless Claude using the operators own interactive Claude Code OAuth login (rather than a stored API key) will have every headless call fail to authenticate under `--bare`.

Discovered debugging `lib/ai/claudeRun.ts`'s `runClaude()` in the Vinnstack repo — a BDD test-implementation skill run kept dying with "Implementation produced no usable result" because the users own `simplify-gate.ps1`/`reusable-gate.ps1` hooks (wired to PostToolUse on Edit|Write|MultiEdit in `~/.claude/settings.json`) intercepted the runs Write calls and the model responded to the gate instead of emitting its JSON contract.

## Related
[[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]
[[--bare forces API-key-only auth in Claude Code]]
