---
ai_hash: 67ee12ae73add6fb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities:
- AppsFlyer connector
- S3 config
- vStorage
- VSTORAGE_* env vars
- MINIO_* env vars
- VSTORAGE_ENDPOINT
- VSTORAGE_ACCESS_KEY
- VSTORAGE_SECRET_KEY
- VSTORAGE_BUCKET
- common/s3/config.py::S3Config.from_env
- AWS_REGION
- boto3
- us-east-1
- APPSFLYER_RAW_STORE
- APPSFLYER_CDP_STORE
- s3
- S3 protocol
- Kafka sink
- S3 raw landing
- leocdp
- HCM04
- hcm04.vstorage.vngcloud.vn
- demo-leocdp
- hcm03
- appsflyer-data
- GreenNode console
- S3 keys
- raw/
- cdp/
- S3-mode landing
- credential naming
source: session 2026-07-03
status: seedling
tags:
- leo-cdp
- appsflyer
- vstorage
- config-rename
title: AppsFlyer connector S3 config is vStorage-only - VSTORAGE_* env vars
type: lesson
---

# AppsFlyer connector S3 config is vStorage-only - VSTORAGE_* env vars

Decision (2026-07-03): the connector's object-storage config is vStorage-only by declaration — env vars renamed `MINIO_*` → `VSTORAGE_*` (`VSTORAGE_ENDPOINT/ACCESS_KEY/SECRET_KEY/BUCKET`, read in `common/s3/config.py::S3Config.from_env`), `AWS_REGION` dropped from the env surface (the boto3 region stays an internal `us-east-1` default — vStorage ignores it). The store toggle values stay `s3` (`APPSFLYER_RAW_STORE=s3`, `APPSFLYER_CDP_STORE=s3`) because vStorage speaks the S3 protocol — only the credential naming was MinIO-flavored noise. Anyone with an old `.env` must rename the four MINIO_* keys or S3-mode landing silently sees empty creds and fails the fail-loud guard.

Related: [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]

## Related

- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]


Verified live 2026-07-04: the real vStorage project is `leocdp` in region **HCM04** (endpoint `https://hcm04.vstorage.vngcloud.vn`), bucket `demo-leocdp` — the previously configured `hcm03` endpoint and `appsflyer-data` bucket never existed. S3 keys are generated per-project in the GreenNode console: vStorage → project list → expand row → "List of S3 keys" → Generate s3 key. Raw CSV lands under `raw/`, CDP JSONL under `cdp/`.

%% ai-graph-start %%

**Related notes:**
- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]
- [[VNG Cloud vStorage is a drop-in S3 backend for path-style S3 clients (MinIO swap)]]
- [[Terraform S3 backend on a non-AWS store (vStorageMinIO) needs skip-checks + path-style]]

**Relations:**
- AppsFlyer connector — *uses* — S3 config
- S3 config — *is* — vStorage-only
- S3 config — *uses* — VSTORAGE_* env vars
- VSTORAGE_* env vars — *renamed from* — MINIO_* env vars
- VSTORAGE_* env vars — *includes* — VSTORAGE_ENDPOINT
- VSTORAGE_* env vars — *includes* — VSTORAGE_ACCESS_KEY
- VSTORAGE_* env vars — *includes* — VSTORAGE_SECRET_KEY
- VSTORAGE_* env vars — *includes* — VSTORAGE_BUCKET
- common/s3/config.py::S3Config.from_env — *reads* — VSTORAGE_* env vars
- AWS_REGION — *dropped from* — env surface
- boto3 — *uses default region* — us-east-1
- vStorage — *ignores* — us-east-1
- vStorage — *speaks* — S3 protocol
- APPSFLYER_RAW_STORE — *set to* — s3
- APPSFLYER_CDP_STORE — *set to* — s3
- AppsFlyer connector — *dropped* — Kafka sink
- AppsFlyer connector — *dropped* — S3 raw landing
- leocdp — *is a project in* — vStorage
- leocdp — *is in region* — HCM04
- HCM04 — *has endpoint* — hcm04.vstorage.vngcloud.vn
- demo-leocdp — *is bucket for* — leocdp
- hcm03 — *never existed* — endpoint
- appsflyer-data — *never existed* — bucket
- S3 keys — *generated in* — GreenNode console
- Raw CSV — *lands under* — raw/
- CDP JSONL — *lands under* — cdp/
- S3-mode landing — *fails on* — empty creds
- credential naming — *was* — MinIO-flavored noise
- MINIO_* env vars — *were* — MinIO-flavored noise

%% ai-graph-end %%