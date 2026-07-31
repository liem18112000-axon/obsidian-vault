---
title: "AppsFlyer connector S3 config is vStorage-only - VSTORAGE_* env vars"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [leo-cdp, appsflyer, vstorage, config-rename]
---

# AppsFlyer connector S3 config is vStorage-only - VSTORAGE_* env vars

Decision (2026-07-03): the connector's object-storage config is vStorage-only by declaration — env vars renamed `MINIO_*` → `VSTORAGE_*` (`VSTORAGE_ENDPOINT/ACCESS_KEY/SECRET_KEY/BUCKET`, read in `common/s3/config.py::S3Config.from_env`), `AWS_REGION` dropped from the env surface (the boto3 region stays an internal `us-east-1` default — vStorage ignores it). The store toggle values stay `s3` (`APPSFLYER_RAW_STORE=s3`, `APPSFLYER_CDP_STORE=s3`) because vStorage speaks the S3 protocol — only the credential naming was MinIO-flavored noise. Anyone with an old `.env` must rename the four MINIO_* keys or S3-mode landing silently sees empty creds and fails the fail-loud guard.

Related: [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]

## Related

- [[AppsFlyer connector dropped Kafka sink and S3 raw landing]]


Verified live 2026-07-04: the real vStorage project is `leocdp` in region **HCM04** (endpoint `https://hcm04.vstorage.vngcloud.vn`), bucket `demo-leocdp` — the previously configured `hcm03` endpoint and `appsflyer-data` bucket never existed. S3 keys are generated per-project in the GreenNode console: vStorage → project list → expand row → "List of S3 keys" → Generate s3 key. Raw CSV lands under `raw/`, CDP JSONL under `cdp/`.
