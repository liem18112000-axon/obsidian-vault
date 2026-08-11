---
ai_hash: 3673cffb56294e46
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26 feat/appsflyer-push-layer
status: seedling
tags:
- appsflyer
- leo-cdp
- idempotency
- streaming
- connector
title: AppsFlyer Push layer appends per-event while Pull replaces the day
type: lesson
---

# AppsFlyer Push layer appends per-event while Pull replaces the day

AppsFlyer raw data can reach Leo CDP two ways, and the storage write differs:

- **Pull layer** (`leo-appsflyer`) *polls* AppsFlyer one day at a time and owns that day wholesale → `sink.write_partition` with `replace:true` on the first batch, so a re-pull/backfill overwrites the partition.
- **Push layer** (`leo-appsflyer-push`) *receives* AppsFlyer's real-time POSTs; each request carries only a few just-pushed events → `sink.append_partition` into `{app_id}__push__{UTC-date}`. It must NOT replace, or each push would erase the day's prior pushes.

Because AppsFlyer Push delivery is **at-least-once**, append must be idempotent **per event**: every event has a `dedupe_key` (sha256 of its canonical sorted JSON). The file sink dedupes against lines already on disk (a stored line == `ev.to_json()`, so `sha256(line) == dedupe_key`); the HTTP sink posts `replace:false` + `dedupe_key` for server-side upsert.

Lesson: streaming ingestion and batch ingestion need different sink contracts — don't reuse a replace-the-partition write for a stream, and put idempotency at the event grain, not the partition grain.

Everything after mapping is shared (`normalize_row → row_to_cdp_event → CdpSink`), keeping Push/Pull at parity. See [[AppsFlyer Push API is the inverse of the Pull API]].

## Related

- [[AppsFlyer Push API is the inverse of the Pull API]]

%% ai-graph-start %%

**Related notes:**
- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[Kafka sink append-only log, idempotency via dedupe_key message key]]
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[AppsFlyer Push API is the inverse of the Pull API]]

%% ai-graph-end %%