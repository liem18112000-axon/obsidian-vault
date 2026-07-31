---
title: "Snapshot a shared dict on the event-loop thread before asyncio.to_thread"
created: 2026-06-15
type: lesson
status: seedling
source: "fb-info-project pause/resume, 2026-06-15"
tags: [python, asyncio, concurrency, gotcha, threading]
---

# Snapshot a shared dict on the event-loop thread before asyncio.to_thread

When an asyncio coroutine hands a shared mutable structure to a worker thread via `await asyncio.to_thread(fn, data)`, **build the snapshot copy on the event-loop thread, synchronously, before the to_thread call** — not inside the worker. Otherwise concurrent coroutines can mutate the structure while the worker iterates it, raising `RuntimeError: dictionary changed size during iteration` (or yielding a torn copy).

**Why the event-loop snapshot is safe:** asyncio is cooperatively scheduled — other coroutines only run at `await` points. A plain `snap = dict(cache)` contains no `await`, so no other coroutine can run *during* the copy; it is effectively atomic. Once you hold `snap`, it is a private object and safe to pass into the worker thread.

```python
# WRONG: worker thread copies while fetch coroutines mutate 'cache'
await asyncio.to_thread(lambda: persist(dict(cache)))   # tears under concurrency
# RIGHT: snapshot on the loop thread first, then hand the snapshot off
snap = dict(cache)                  # atomic w.r.t. coroutines (no await)
await asyncio.to_thread(persist, snap)
```

**Context:** in the fb-info-project scraper, profile-fetch coroutines mutate a run-wide cache dict; a periodic checkpoint writes that cache to disk off the event loop via to_thread. Snapshotting in the loop body fixed the race.

General rule: the GIL does not protect a multi-statement iteration in another thread; cooperative single-threaded copy on the loop does.

## Related

- [[A persisted dedup cache doubles as a resume log]]
