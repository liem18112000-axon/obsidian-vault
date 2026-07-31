---
ai_hash: c40c61c449d618e0
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities:
- AppsFlyer connector
- JSONL file-S3 sink
- Kafka removal
- appsflyer-data-connector
- JSONL file
- local dir
- S3
- vStorage
- APPSFLYER_CDP_STORE
- CdpHttpSink
- --sink cdp
- LEO_CDP_API_URL
- LEO_CDP_TOKEN_*
- CDP REST examples
- dummy_cdp_endpoint
- list_all_events
- push_to_data_observer
- MinIO compose profile
- MINIO_ROOT_*
- APPSFLYER_LANDING_DIR
- APPSFLYER_CDP_OUT_DIR
- Leo CDP
- per-event REST POSTs
- Push receiver
- AppsFlyer
- dedupe
- docs/deploy/
- AppsFlyer connector dropped Kafka sink and S3 raw landing
source: session 2026-07-03
status: seedling
tags:
- leo-cdp
- appsflyer
- design-decision
- sink
title: AppsFlyer connector reduced to a single JSONL file-S3 sink
type: lesson
---

# AppsFlyer connector reduced to a single JSONL file-S3 sink

Decision (2026-07-03, follow-up to the Kafka removal): the appsflyer-data-connector now has a single sink family — JSONL `file` (local dir, or S3/vStorage via `APPSFLYER_CDP_STORE=s3`). Removed: `CdpHttpSink` (`--sink cdp`, `LEO_CDP_API_URL`/`LEO_CDP_TOKEN_*`), the three CDP REST examples (dummy_cdp_endpoint, list_all_events, push_to_data_observer), the local MinIO compose profile + `MINIO_ROOT_*` creds, and the `APPSFLYER_LANDING_DIR`/`APPSFLYER_CDP_OUT_DIR` env lines (code defaults `./data/landing` / `./data/cdp` apply). Leo CDP now ingests from the landed JSONL rather than per-event REST POSTs. Push receiver durability unchanged in principle: failed write → 5xx → AppsFlyer redelivers; dedupe happens against lines already in the JSONL. Deploy docs under docs/deploy/ still describe `--sink cdp` and are pending a rewrite.

Related: [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]

## Related

- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[Identity-keyed CDP API breaks content-hash idempotency]]
- [[AppsFlyer package layout package-per-concern with no loose modules]]
- [[AppsFlyer Push layer appends per-event while Pull replaces the day]]

**Relations:**
- AppsFlyer connector — *reduced to* — JSONL file-S3 sink
- appsflyer-data-connector — *has single sink family* — JSONL file
- JSONL file — *can be stored in* — local dir
- JSONL file — *can be stored in* — S3
- JSONL file — *can be stored in* — vStorage
- S3 — *configured via* — APPSFLYER_CDP_STORE
- vStorage — *configured via* — APPSFLYER_CDP_STORE
- appsflyer-data-connector — *removed* — CdpHttpSink
- CdpHttpSink — *used option* — --sink cdp
- CdpHttpSink — *used setting* — LEO_CDP_API_URL
- CdpHttpSink — *used setting* — LEO_CDP_TOKEN_*
- appsflyer-data-connector — *removed* — CDP REST examples
- CDP REST examples — *includes* — dummy_cdp_endpoint
- CDP REST examples — *includes* — list_all_events
- CDP REST examples — *includes* — push_to_data_observer
- appsflyer-data-connector — *removed* — MinIO compose profile
- MinIO compose profile — *used setting* — MINIO_ROOT_*
- appsflyer-data-connector — *removed* — APPSFLYER_LANDING_DIR
- appsflyer-data-connector — *removed* — APPSFLYER_CDP_OUT_DIR
- Leo CDP — *ingests from* — JSONL file
- Leo CDP — *previously ingested via* — per-event REST POSTs
- Push receiver — *has* — unchanged durability
- AppsFlyer — *triggers* — redelivery on failed write
- dedupe — *occurs against* — JSONL file
- docs/deploy/ — *describes* — --sink cdp
- docs/deploy/ — *needs* — rewrite
- AppsFlyer connector reduced to a single JSONL file-S3 sink — *is a follow-up to* — Kafka removal
- AppsFlyer connector reduced to a single JSONL file-S3 sink — *is related to* — AppsFlyer connector dropped Kafka sink and S3 raw landing

%% ai-graph-end %%