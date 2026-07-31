---
title: "MinIO server creds (ROOT_USER/PASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEY/SECRET_KEY)"
created: 2026-06-30
type: lesson
status: seedling
source: "session 2026-06-30; raw-landing-to-vStorage feature"
tags: [minio, s3, vstorage, config, gotcha, boto3]
---

# MinIO server creds (ROOT_USER/PASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEY/SECRET_KEY)

In this project (appsflyer-data-connector) the MinIO/vStorage env vars split into two groups that are easy to confuse:

- **Server creds** — `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD`: what the MinIO *container* boots with (set in docker-compose.minio.yml).
- **S3 client creds** — `MINIO_ACCESS_KEY` / `MINIO_SECRET_KEY`: what `S3Config.from_env()` (common/s3.py) reads to authenticate the boto3 client. Plus `MINIO_ENDPOINT`, `MINIO_BUCKET`, `AWS_REGION`.

For MinIO the root user/password ARE the default access/secret keys, but they are **separate env var names** — setting only `MINIO_ROOT_*` leaves `S3Config` with empty creds and the client effectively unauthenticated. So any feature that uploads to vStorage (e.g. `APPSFLYER_RAW_STORE=s3` raw landing) must have `MINIO_ACCESS_KEY`/`MINIO_SECRET_KEY` set explicitly, not just the ROOT vars.

Design note from the same feature: boto3's S3 client has two upload styles — `upload_fileobj(Fileobj=...)` for an **unbuffered stream** (the puller streams the API response body straight through) and `put_object(Body=bytes)` for an **already-in-memory body** (the day's raw CSV, serialized with csv.DictWriter into a StringIO). Pick by whether the body is already materialized. Related: [[Leo CDP public REST API contract]].

## Related

- [[Leo CDP public REST API contract]]
