---
title: "Postgres partitioned-table UNIQUE index must include the partition key"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25 (leo-customer360)"
tags: [postgres, partitioning, unique-constraint, idempotency, gotcha]
---

# Postgres partitioned-table UNIQUE index must include the partition key

In PostgreSQL, any UNIQUE constraint or UNIQUE index on a partitioned table **must include all partition-key columns**. A plain unique index on just a business key is rejected.

Example: a high-volume event table `PARTITION BY RANGE (event_time)` cannot have `UNIQUE (tenant_id, source_system, event_dedup_key)` — it must be `UNIQUE (tenant_id, source_system, event_dedup_key, event_time)`.

**Consequence for idempotent upserts:** the natural dedup key has to be paired with the partition key, so `ON CONFLICT` only prevents duplicates that share the *same* `event_time`. If a retry could re-derive a slightly different `event_time`, dedup silently fails. Design the ingest so the dedup-relevant timestamp is deterministic (carry the source/received timestamp, do not use `now()` at insert), or dedup upstream (e.g. Redis Streams consumer-group ACK) instead of relying on a DB unique constraint.

Surfaced designing the CDP cdp_raw_events Loader.

## Related

- [[Redis Streams consumer-group at-least-once Loader pattern]]
