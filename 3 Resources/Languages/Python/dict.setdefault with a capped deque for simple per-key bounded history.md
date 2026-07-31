---
title: "dict.setdefault with a capped deque for simple per-key bounded history"
created: 2026-07-11
type: technique
status: seedling
source: "virtual-avatar session 2026-07-11, app/llm.py"
tags: [python, deque, setdefault, conversation-history, state-management]
---

# dict.setdefault with a capped deque for simple per-key bounded history

For a lightweight, single-process, per-key bounded history (e.g. last N turns of a conversation, keyed by a client-generated ID), `dict.setdefault(key, deque(maxlen=N))` is a clean one-liner:

```python
histories: dict[str, deque[dict]] = {}
history = histories.setdefault(conversation_id, deque(maxlen=8))
history.append(new_item)  # oldest auto-evicted once maxlen is hit
```

`setdefault` returns the existing deque if the key is already present, or inserts and returns a fresh capped one if not — no `if key not in dict: dict[key] = ...` boilerplate. The `deque(maxlen=N)` handles the bounding itself (silently drops the oldest item on append once full), so no manual trim/slice step is needed.

This is the right level of engineering for a single-process app with no real persistence/multi-instance requirement (e.g. a live-talk Q&A bot that only needs to remember the current session's last few exchanges) — reaching for Redis/a database for this would be over-engineering. The explicit tradeoff to document: this state lives in one process's memory, so it silently fragments/resets across process restarts or multiple concurrent instances (e.g. a horizontally-scaled deployment) — fine when "one active session at a time" is a true assumption of the system, not fine otherwise.

Used in the virtual-avatar project (app/llm.py) to give live Q&A follow-up questions ("what about X") access to the last 4 exchanges of conversation history, without needing per-visitor server-side sessions beyond a client-generated conversation ID header.
