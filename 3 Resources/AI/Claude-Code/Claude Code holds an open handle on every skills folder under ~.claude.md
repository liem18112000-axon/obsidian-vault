---
ai_hash: f190b156dc01e0f7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- claude-code
- gotcha
- windows
- file-lock
title: Claude Code holds an open handle on every skills folder under ~/.claude
type: lesson
---

# Claude Code holds an open handle on every skills folder under ~/.claude

Claude Code runs a directory watcher that keeps an open OS handle on any folder named `skills` under `~/.claude` (it watches for skill changes to hot-reload them). Consequence: if you place a temporary git clone under `~/.claude` and it contains a `skills/` subfolder, that subfolder cannot be deleted (Windows: "being used by another process"; the dir move/rename also fails) until Claude Code restarts and releases the handle.

Practical fix: do scratch clones **outside** `~/.claude` (e.g. system `$TEMP`), or accept the empty locked `skills` dir as harmless and let it clear on the next restart. `cmd //c "rmdir /s /q ..."` will delete everything except the locked `skills` node itself.

Related: [[MCP servers load only at Claude Code startup; skills hot-reload]].

## Related

- [[MCP servers load only at Claude Code startup; skills hot-reload]]

%% ai-graph-start %%

**Related notes:**
- [[MCP servers load only at Claude Code startup; skills hot-reload]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]
- [[Windows claude subprocess is a process tree — taskkill T to reap it]]

%% ai-graph-end %%