---
ai_hash: c938c793f915a8e1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities:
- MinIO
- S3
- ROOT_USER
- ROOT_PASSWORD
- ACCESS_KEY
- SECRET_KEY
- MINIO_ROOT_USER
- MINIO_ROOT_PASSWORD
- MINIO_ACCESS_KEY
- MINIO_SECRET_KEY
- appsflyer-data-connector
- S3Config
- common/s3.py
- boto3 client
- MINIO_ENDPOINT
- MINIO_BUCKET
- AWS_REGION
- docker-compose.minio.yml
- upload_fileobj
- put_object
- unbuffered stream
- already-in-memory body
- APPSFLYER_RAW_STORE
- vStorage
- Leo CDP public REST API contract
- Server creds
- S3 client creds
- MinIO container
- unauthenticated client
- S3Config with empty creds
- design note
source: session 2026-06-30; raw-landing-to-vStorage feature
status: seedling
tags:
- minio
- s3
- vstorage
- config
- gotcha
- boto3
title: MinIO server creds (ROOT_USER/PASSWORD) are distinct env vars from the S3 client
  creds (ACCESS_KEY/SECRET_KEY)
type: lesson
---

# MinIO server creds (ROOT_USER/PASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEY/SECRET_KEY)

In this project (appsflyer-data-connector) the MinIO/vStorage env vars split into two groups that are easy to confuse:

- **Server creds** — `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD`: what the MinIO *container* boots with (set in docker-compose.minio.yml).
- **S3 client creds** — `MINIO_ACCESS_KEY` / `MINIO_SECRET_KEY`: what `S3Config.from_env()` (common/s3.py) reads to authenticate the boto3 client. Plus `MINIO_ENDPOINT`, `MINIO_BUCKET`, `AWS_REGION`.

For MinIO the root user/password ARE the default access/secret keys, but they are **separate env var names** — setting only `MINIO_ROOT_*` leaves `S3Config` with empty creds and the client effectively unauthenticated. So any feature that uploads to vStorage (e.g. `APPSFLYER_RAW_STORE=s3` raw landing) must have `MINIO_ACCESS_KEY`/`MINIO_SECRET_KEY` set explicitly, not just the ROOT vars.

Design note from the same feature: boto3's S3 client has two upload styles — `upload_fileobj(Fileobj=...)` for an **unbuffered stream** (the puller streams the API response body straight through) and `put_object(Body=bytes)` for an **already-in-memory body** (the day's raw CSV, serialized with csv.DictWriter into a StringIO). Pick by whether the body is already materialized. Related: [[Leo CDP public REST API contract]].

## Related

- [[Leo CDP public REST API contract]]

%% ai-graph-start %%

**Related notes:**
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[Separate docker-compose files are isolated networks; use one file + a profile for optional services]]
- [[AppsFlyer connector reduced to a single JSONL file-S3 sink]]
- [[Module-level load_dotenv lets unit tests hit real cloud credentials]]
- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]

**Relations:**
- Server creds — *consists of* — MINIO_ROOT_USER
- Server creds — *consists of* — MINIO_ROOT_PASSWORD
- S3 client creds — *consists of* — MINIO_ACCESS_KEY
- S3 client creds — *consists of* — MINIO_SECRET_KEY
- Server creds — *are distinct from* — S3 client creds
- MinIO container — *boots with* — Server creds
- Server creds — *set in* — docker-compose.minio.yml
- S3 client creds — *read by* — S3Config
- S3Config — *from_env method in* — common/s3.py
- S3Config — *authenticates* — boto3 client
- S3 client creds — *include* — MINIO_ENDPOINT
- S3 client creds — *include* — MINIO_BUCKET
- S3 client creds — *include* — AWS_REGION
- MinIO — *default ACCESS_KEY is* — MINIO_ROOT_USER
- MinIO — *default SECRET_KEY is* — MINIO_ROOT_PASSWORD
- MINIO_ROOT_USER — *is a* — ROOT_USER
- MINIO_ROOT_PASSWORD — *is a* — ROOT_PASSWORD
- MINIO_ACCESS_KEY — *is a* — ACCESS_KEY
- MINIO_SECRET_KEY — *is a* — SECRET_KEY
- Setting only MINIO_ROOT_* — *leaves* — S3Config with empty creds
- S3Config with empty creds — *results in* — unauthenticated client
- APPSFLYER_RAW_STORE — *requires* — MINIO_ACCESS_KEY
- APPSFLYER_RAW_STORE — *requires* — MINIO_SECRET_KEY
- APPSFLYER_RAW_STORE — *uploads to* — vStorage
- boto3 client — *has upload style* — upload_fileobj
- boto3 client — *has upload style* — put_object
- upload_fileobj — *for* — unbuffered stream
- put_object — *for* — already-in-memory body
- Leo CDP public REST API contract — *is related to* — design note
- appsflyer-data-connector — *is a project that uses* — MinIO
- MinIO — *is a* — vStorage
- S3 — *is a* — vStorage
- APPSFLYER_RAW_STORE — *is a* — raw landing
- appsflyer-data-connector — *uses* — vStorage

%% ai-graph-end %%