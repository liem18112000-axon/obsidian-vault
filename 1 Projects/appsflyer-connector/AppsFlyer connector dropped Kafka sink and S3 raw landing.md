---
ai_hash: 2c5ef82ed63e3acd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities:
- appsflyer-data-connector
- Kafka sink
- S3 raw landing
- file sink
- JSONL
- S3
- APPSFLYER_CDP_STORE
- cdp sink
- Leo CDP REST /api/event/save
- KafkaSink
- S3 raw-CSV landing toggle
- APPSFLYER_RAW_STORE
- APPSFLYER_RAW_S3_PREFIX
- build_raw_store
- S3RawCsvStore
- raw CSV
- LocalRawStore
- Kafka
- confluent-kafka
- Terraform
- Push receiver
- AppsFlyer's own redelivery
- CDP POST
- dedupe_key
- common/s3/S3RawStore
- CDP JSONL S3 sink
- appsflyer-puller
- RawStore protocol
- raw_store= injection point
- run_day
- Grep-audit env vars against code before pruning .env files
source: session 2026-07-03
status: seedling
tags:
- leo-cdp
- appsflyer
- design-decision
- kafka
- s3
title: AppsFlyer connector dropped Kafka sink and S3 raw landing
type: lesson
---

# AppsFlyer connector dropped Kafka sink and S3 raw landing

Decision (2026-07-03): the appsflyer-data-connector was slimmed to two sinks — `file` (JSONL, optionally to S3 via `APPSFLYER_CDP_STORE=s3`) and `cdp` (Leo CDP REST `/api/event/save`). The `KafkaSink` and the S3 raw-CSV landing toggle (`APPSFLYER_RAW_STORE` / `APPSFLYER_RAW_S3_PREFIX`, `build_raw_store`, `S3RawCsvStore`) were deleted; raw CSV now always lands locally via `LocalRawStore`.

**Why:** Kafka was never wired to a real broker in this deployment, and its Terraform-based deploy path had already been removed — the sink was dead weight (extra dependency `confluent-kafka`, extra env, extra tests). With Kafka gone, the Push receiver's durability story inverts: instead of "publish to a durable topic and return", it relies on **AppsFlyer's own redelivery** — a failed CDP POST surfaces as 5xx and AppsFlyer retries; idempotency per `dedupe_key` makes retries safe.

Kept on purpose: `common/s3/S3RawStore` (generic S3 wrapper) still backs the CDP JSONL S3 sink and the standalone streaming `appsflyer-puller`, so removing the raw-landing *toggle* did not remove the S3 plumbing.

Gotcha: the `RawStore` protocol + `raw_store=` injection point in `run_day` were kept solely for test injection even though only one implementation remains.

Related: [[Grep-audit env vars against code before pruning .env files]]

## Related

- [[Grep-audit env vars against code before pruning .env files]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[Kafka sink append-only log, idempotency via dedupe_key message key]]
- [[AppsFlyer package layout package-per-concern with no loose modules]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]

**Relations:**
- appsflyer-data-connector — *dropped* — Kafka sink
- appsflyer-data-connector — *dropped* — S3 raw landing
- appsflyer-data-connector — *slimmed to include* — file sink
- appsflyer-data-connector — *slimmed to include* — cdp sink
- file sink — *outputs* — JSONL
- file sink — *can store to* — S3
- S3 — *configured by* — APPSFLYER_CDP_STORE
- cdp sink — *sends to* — Leo CDP REST /api/event/save
- KafkaSink — *was deleted from* — appsflyer-data-connector
- S3 raw-CSV landing toggle — *was deleted from* — appsflyer-data-connector
- S3 raw-CSV landing toggle — *comprises* — APPSFLYER_RAW_STORE
- S3 raw-CSV landing toggle — *comprises* — APPSFLYER_RAW_S3_PREFIX
- S3 raw-CSV landing toggle — *comprises* — build_raw_store
- S3 raw-CSV landing toggle — *comprises* — S3RawCsvStore
- raw CSV — *lands via* — LocalRawStore
- Kafka — *was not wired to* — real broker
- Kafka — *deploy path removed from* — Terraform
- Kafka sink — *was considered* — dead weight
- dead weight — *due to dependency* — confluent-kafka
- dead weight — *due to* — extra env
- dead weight — *due to* — extra tests
- Push receiver — *relies on* — AppsFlyer's own redelivery
- AppsFlyer's own redelivery — *handles* — failed CDP POST
- AppsFlyer — *retries* — failed CDP POST
- idempotency per dedupe_key — *makes safe* — retries
- common/s3/S3RawStore — *backs* — CDP JSONL S3 sink
- common/s3/S3RawStore — *backs* — appsflyer-puller
- RawStore protocol — *kept for* — test injection
- raw_store= injection point — *kept for* — test injection
- run_day — *uses* — raw_store= injection point
- AppsFlyer connector dropped Kafka sink and S3 raw landing — *is related to* — Grep-audit env vars against code before pruning .env files

%% ai-graph-end %%