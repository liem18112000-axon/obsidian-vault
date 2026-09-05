---
title: "Broker Redis needs opposite config from cache Redis, run it separately"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25 (leo-customer360 data-tracking-api)"
tags: [redis, streams, architecture, gotcha]
---

# Broker Redis needs opposite config from cache Redis, run it separately

An event-stream **broker** Redis and an API **cache** Redis want opposite configuration, so run them as two separate instances rather than sharing one.

- **Cache** Redis: LRU eviction (`maxmemory-policy allkeys-lru`/`volatile-lru`), TTLs, small footprint — losing a cold key is fine.
- **Broker** Redis (Redis Streams event buffer): `appendonly yes` (AOF, `everysec`) so a restart does not lose buffered-but-unconsumed events, and `maxmemory-policy noeviction` so a write flood is refused rather than silently dropping un-consumed stream entries. Cap growth with `XADD ... MAXLEN ~ N`.

**Why separate:** a high-throughput event flood on a shared instance can evict auth tokens / session cache (opposite of what you want), or OOM the cache. Sharing at most via a separate logical DB is a weak fallback; a dedicated instance is the real fix. Co-locate the broker on the producer box so the hot-path `XADD` stays on loopback (sub-ms) and ingestion is self-contained.

Surfaced wiring the CDP data-tracking-api broker on its own vServer.

## Related

- [[Redis Streams consumer-group at-least-once Loader pattern]]
