---
title: "--bare forces API-key-only auth in Claude Code"
created: 2026-07-11
type: lesson
status: seedling
source: "Vinnstack BDD implement-stage debugging, 2026-07-11"
tags: [claude-code, cli-flags, auth, gotcha]
---

# --bare forces API-key-only auth in Claude Code

Claude Codes `--bare` CLI flag is documented as "minimal mode: skip hooks, LSP, plugin sync, attribution, auto-memory, background prefetches, keychain reads, and CLAUDE.md auto-discovery" — it reads like a generic "run lean/skip hooks" switch.

The important side effect buried in that list is **"keychain reads"**: `--bare` also sets `CLAUDE_CODE_SIMPLE=1` and restricts Anthropic auth to strictly `ANTHROPIC_API_KEY` or an `apiKeyHelper` (settable via `--settings`) — it does NOT fall back to the normal interactive OAuth session / OS keychain credential that a regular `claude` login uses.

So for any process that spawns headless `claude -p` calls relying on the users existing Claude Code OAuth login (no `ANTHROPIC_API_KEY` env var, by design — e.g. to avoid accidentally billing through a stray system API key), adding `--bare` to "fix" hook interference will instead break authentication outright, since there is no API key to fall back to.

If the goal is only to skip the users personal hooks (not LSP/memory/etc.), use `--setting-sources project,local` instead — see [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]].
