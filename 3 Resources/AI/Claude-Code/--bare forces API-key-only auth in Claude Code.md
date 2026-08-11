---
ai_hash: 0cff119eec133044
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: Vinnstack BDD implement-stage debugging, 2026-07-11
status: seedling
tags:
- claude-code
- cli-flags
- auth
- gotcha
title: --bare forces API-key-only auth in Claude Code
type: lesson
---

# --bare forces API-key-only auth in Claude Code

Claude Codes `--bare` CLI flag is documented as "minimal mode: skip hooks, LSP, plugin sync, attribution, auto-memory, background prefetches, keychain reads, and CLAUDE.md auto-discovery" — it reads like a generic "run lean/skip hooks" switch.

The important side effect buried in that list is **"keychain reads"**: `--bare` also sets `CLAUDE_CODE_SIMPLE=1` and restricts Anthropic auth to strictly `ANTHROPIC_API_KEY` or an `apiKeyHelper` (settable via `--settings`) — it does NOT fall back to the normal interactive OAuth session / OS keychain credential that a regular `claude` login uses.

So for any process that spawns headless `claude -p` calls relying on the users existing Claude Code OAuth login (no `ANTHROPIC_API_KEY` env var, by design — e.g. to avoid accidentally billing through a stray system API key), adding `--bare` to "fix" hook interference will instead break authentication outright, since there is no API key to fall back to.

If the goal is only to skip the users personal hooks (not LSP/memory/etc.), use `--setting-sources project,local` instead — see [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]].

%% ai-graph-start %%

**Related notes:**
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]
- [[Claude Code headless auth setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN]]
- [[Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI]]
- [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]]
- [[Enable Claude Code fast mode in headless -p runs via --settings fastMode]]

%% ai-graph-end %%