---
title: "Redis Streams consumer-group at-least-once Loader pattern"
created: 2026-08-25
type: howto
status: seedling
source: "session 2026-08-25 (leo-customer360 event_loader)"
tags: [redis, streams, consumer-group, idempotency, dagster]
---

# Redis Streams consumer-group at-least-once Loader pattern

A durable, at-least-once Redis Streams consumer that survives crashes and consumer death, without losing or (permanently) duplicating work.

**Cycle per worker tick:**
1. `XGROUP CREATE <stream> <group> id=0 MKSTREAM` once; swallow the `BUSYGROUP` error so it is idempotent (also creates the stream if absent).
2. `XAUTOCLAIM <stream> <group> <consumer> <min-idle-ms> 0-0 COUNT n` **first** — reclaim entries a dead/stuck consumer left pending (delivered-but-never-ACKed).
3. Then `XREADGROUP <group> <consumer> COUNT n BLOCK ms STREAMS <stream> '>'` for brand-new entries.
4. Process the batch, then `XACK <stream> <group> <ids...>` **only after** the downstream commit (e.g. the Postgres transaction `commit()`).

**Why ACK-after-commit:** a crash between the DB commit and the ACK re-delivers that batch on the next tick → **at-least-once**. Make the downstream write idempotent (e.g. `ON CONFLICT DO NOTHING` on a dedup key) to get effectively-once. ACK-before-commit would instead risk silent data loss.

Runs cleanly as a frequently-scheduled Dagster op/sensor tick rather than a bare `while True` loop. Surfaced building the CDP web-tracking event Loader (backend-system/event_loader).

## Related

- [[Broker Redis needs opposite config from cache Redis]]
- [[run it separately]]
