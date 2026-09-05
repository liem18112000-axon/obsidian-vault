---
title: "Run an event-broker Redis as a dedicated co-located instance, not the cache Redis"
created: 2026-08-25
type: lesson
status: seedling
source: "LEOCDP web-tracking plan, session 2026-08-25"
tags: [redis, redis-streams, event-broker, architecture, gotcha, ingestion]
---

# Run an event-broker Redis as a dedicated co-located instance, not the cache Redis

When Redis is the **event broker** (e.g. Redis Streams in front of an ingestion pipeline), run it as a **dedicated instance**, and prefer **co-locating it on the producer's box** — do NOT reuse the app's cache/session Redis.

**Why isolate from the cache Redis:** the two roles have opposite operational profiles. A cache/session store is small, latency-sensitive, and safely LRU-evictable; an event broker is high write-throughput with a large/growing stream that must **never** evict un-consumed entries and wants persistence. Sharing one instance means an event flood can evict auth tokens / session data or OOM the cache. (If you truly cannot add an instance, at least use a separate logical DB with `maxmemory-policy noeviction` for the stream — but a separate instance is far safer.)

**Why co-locate with the producer:** the hot-path `XADD` stays on loopback → sub-millisecond, with no cross-box network dependency in the path that returns the ack (e.g. an HTTP 204). It also makes ingestion self-contained: the producer + broker keep accepting traffic even if the DB/consumer/other boxes are down.

**Durability config for the broker role:**
- `appendonly yes` (AOF, `everysec`) so a restart doesn't lose buffered-but-unconsumed entries.
- `maxmemory-policy noeviction`; cap growth with `XADD ... MAXLEN ~ <N>` and **alert on stream length + consumer/flusher lag**.
- Consumer group + `XAUTOCLAIM` to recover pending entries from a dead consumer.
- Keep an object-storage lake as the true durable record; then AOF only shrinks the crash loss/replay window rather than being the sole safety net.

**Memory sizing rule of thumb:** `stream RAM ≈ peak_eps × buffer_seconds × avg_event_bytes × ~1.5 overhead`. E.g. 5,000 eps × 120 s × 1 KB × 1.5 ≈ ~0.9 GB.

This is the buffer tier of the [[Lightweight-but-scalable web event collector pattern]].

## Related

- [[Lightweight-but-scalable web event collector pattern]]
