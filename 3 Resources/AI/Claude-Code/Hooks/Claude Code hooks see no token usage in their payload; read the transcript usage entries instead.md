---
title: "Claude Code hooks see no token usage in their payload; read the transcript usage entries instead"
created: 2026-06-02
type: lesson
status: seedling
source: "session 2026-06-02 handoff gate"
tags: [claude-code, hooks, context, tokens, transcript]
---

# Claude Code hooks see no token usage in their payload; read the transcript usage entries instead

A Claude Code hook receives only session metadata on stdin (`session_id`, `transcript_path`, `cwd`, `hook_event_name`, `permission_mode`; plus `model` on SessionStart). The payload carries **no token counts and no context-window usage** - so a hook cannot directly compare current usage against a percentage threshold.

**Workaround:** read the file at `transcript_path` (JSONL). Each assistant entry has `message.usage` with `input_tokens`, `cache_read_input_tokens`, and `cache_creation_input_tokens`; their sum is the live context size. Detect the window from the model id (a `1m`/`[1m]` tag means the 1M window, else 200K) and compute the percentage yourself. Walk only the tail (e.g. last 300 lines) for the most recent usage entry to stay cheap.

**How to apply:** this is how `handoff-context-check.ps1` decides when to prompt for a /session-handoff. See [[3 Resources/AI Tools/Claude Code/Hooks/Claude Code hooks cannot run slash commands or clear-compact; they only inject additionalContext]].

## Related

- [[3 Resources/AI Tools/Claude Code/Hooks/Claude Code hooks cannot run slash commands or clear-compact; they only inject additionalContext]]

