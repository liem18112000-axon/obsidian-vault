---
ai_hash: d7446a93db6135ae
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project session 2026-06-11
status: evergreen
tags:
- python
- asyncio
- concurrency
title: asyncio.gather preserves input order in its result list
type: concept
---

# asyncio.gather preserves input order in its result list

`asyncio.gather(*aws)` returns results **in the order the awaitables were passed in**, regardless of which finishes first. Concurrency affects completion timing only, never result position.

Practical consequence: if a list must come out in a specific order after a concurrent fan-out (e.g. profiles sorted newest-comment-first before visiting each profile in parallel), it is sufficient to sort the *input* list — no re-sort or index bookkeeping is needed after gathering.

%% ai-graph-start %%

**Related notes:**
- [[Snapshot a shared dict on the event-loop thread before asyncio.to_thread]]
- [[Crash-safe incremental output as_completed + indexed results + stable filename reused for checkpoint and final]]

%% ai-graph-end %%