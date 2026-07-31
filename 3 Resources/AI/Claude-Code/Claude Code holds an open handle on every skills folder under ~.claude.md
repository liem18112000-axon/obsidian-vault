---
title: "Claude Code holds an open handle on every skills folder under ~/.claude"
created: 2026-06-16
type: lesson
status: seedling
source: "session 2026-06-16"
tags: [claude-code, gotcha, windows, file-lock]
---

# Claude Code holds an open handle on every skills folder under ~/.claude

Claude Code runs a directory watcher that keeps an open OS handle on any folder named `skills` under `~/.claude` (it watches for skill changes to hot-reload them). Consequence: if you place a temporary git clone under `~/.claude` and it contains a `skills/` subfolder, that subfolder cannot be deleted (Windows: "being used by another process"; the dir move/rename also fails) until Claude Code restarts and releases the handle.

Practical fix: do scratch clones **outside** `~/.claude` (e.g. system `$TEMP`), or accept the empty locked `skills` dir as harmless and let it clear on the next restart. `cmd //c "rmdir /s /q ..."` will delete everything except the locked `skills` node itself.

Related: [[MCP servers load only at Claude Code startup; skills hot-reload]].

## Related

- [[MCP servers load only at Claude Code startup; skills hot-reload]]
