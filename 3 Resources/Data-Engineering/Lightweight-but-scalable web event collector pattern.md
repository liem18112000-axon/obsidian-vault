---
title: "Lightweight-but-scalable web event collector pattern"
created: 2026-08-25
type: model
status: seedling
source: "LEOCDP web-tracking plan, session 2026-08-25"
tags: [cdp, event-collection, streaming, architecture, web-tracking, pattern]
---

# Lightweight-but-scalable web event collector pattern

A design pattern for **first-party web tracking** that must start cheap but scale without a rewrite. The core move is to keep every internal interface swappable and make **object storage — not the in-memory buffer — the durable record**.

**Four parts:**
1. **Thin stateless edge Collector.** On the hot path it only validates, enriches server-side (timestamp, geo, UA), hashes PII, appends to a buffer, and returns `204`/1x1 gif. It **never** touches the DB synchronously and holds no session state, so you can run N replicas behind a load balancer. Target < ~5 ms server time.
2. **Swappable durable buffer** behind an `EventSink` interface: **Redis Streams** first (zero new infra), **Kafka/Redpanda** later. Swapping is a config change, not a rewrite.
3. **Raw event lake** on object storage: append-only, gzipped **NDJSON**, partitioned `tenant=/dt=/hour=`. Immutable, cheap, replayable, and directly readable by tools like Airbyte's S3 source. This is the source of truth.
4. **Idempotent Loader** (dedup key) — a micro-batch job first (reusing the existing bulk-ingest logic), a streaming consumer later. Failed batches go to a DLQ bucket; the lake copy is never mutated, so any time range can be replayed deterministically.

**Why:** decoupling collection from processing means a traffic spike can't take down the DB or the app; the lake means nothing is lost if the DB/consumer is down; swappable interfaces mean Phase-1 lightweight → Phase-3 streaming needs no rewrite. Essentially a self-hosted Snowplow/Segment-lite.

**Honesty guardrail:** if the buffer is capacity-capped (e.g. Redis `MAXLEN`), the lake must be the durable record and you must alert on flusher lag — otherwise a capped buffer silently drops data.

Uses [[VNG Cloud vStorage is S3-compatible object storage]] as the lake. Part of [[LEO CDP has two complementary ingestion lanes converging at CIR]].

## Related

- [[VNG Cloud vStorage is S3-compatible object storage]]
- [[LEO CDP has two complementary ingestion lanes converging at CIR]]
