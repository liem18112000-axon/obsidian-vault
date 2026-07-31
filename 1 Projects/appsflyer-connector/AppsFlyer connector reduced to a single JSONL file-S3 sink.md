---
title: "AppsFlyer connector reduced to a single JSONL file-S3 sink"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [leo-cdp, appsflyer, design-decision, sink]
---

# AppsFlyer connector reduced to a single JSONL file-S3 sink

Decision (2026-07-03, follow-up to the Kafka removal): the appsflyer-data-connector now has a single sink family — JSONL `file` (local dir, or S3/vStorage via `APPSFLYER_CDP_STORE=s3`). Removed: `CdpHttpSink` (`--sink cdp`, `LEO_CDP_API_URL`/`LEO_CDP_TOKEN_*`), the three CDP REST examples (dummy_cdp_endpoint, list_all_events, push_to_data_observer), the local MinIO compose profile + `MINIO_ROOT_*` creds, and the `APPSFLYER_LANDING_DIR`/`APPSFLYER_CDP_OUT_DIR` env lines (code defaults `./data/landing` / `./data/cdp` apply). Leo CDP now ingests from the landed JSONL rather than per-event REST POSTs. Push receiver durability unchanged in principle: failed write → 5xx → AppsFlyer redelivers; dedupe happens against lines already in the JSONL. Deploy docs under docs/deploy/ still describe `--sink cdp` and are pending a rewrite.

Related: [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]

## Related

- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
