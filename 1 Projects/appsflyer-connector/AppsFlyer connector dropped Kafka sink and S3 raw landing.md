---
title: "AppsFlyer connector dropped Kafka sink and S3 raw landing"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [leo-cdp, appsflyer, design-decision, kafka, s3]
---

# AppsFlyer connector dropped Kafka sink and S3 raw landing

Decision (2026-07-03): the appsflyer-data-connector was slimmed to two sinks — `file` (JSONL, optionally to S3 via `APPSFLYER_CDP_STORE=s3`) and `cdp` (Leo CDP REST `/api/event/save`). The `KafkaSink` and the S3 raw-CSV landing toggle (`APPSFLYER_RAW_STORE` / `APPSFLYER_RAW_S3_PREFIX`, `build_raw_store`, `S3RawCsvStore`) were deleted; raw CSV now always lands locally via `LocalRawStore`.

**Why:** Kafka was never wired to a real broker in this deployment, and its Terraform-based deploy path had already been removed — the sink was dead weight (extra dependency `confluent-kafka`, extra env, extra tests). With Kafka gone, the Push receiver's durability story inverts: instead of "publish to a durable topic and return", it relies on **AppsFlyer's own redelivery** — a failed CDP POST surfaces as 5xx and AppsFlyer retries; idempotency per `dedupe_key` makes retries safe.

Kept on purpose: `common/s3/S3RawStore` (generic S3 wrapper) still backs the CDP JSONL S3 sink and the standalone streaming `appsflyer-puller`, so removing the raw-landing *toggle* did not remove the S3 plumbing.

Gotcha: the `RawStore` protocol + `raw_store=` injection point in `run_day` were kept solely for test injection even though only one implementation remains.

Related: [[Grep-audit env vars against code before pruning .env files]]

## Related

- [[Grep-audit env vars against code before pruning .env files]]
