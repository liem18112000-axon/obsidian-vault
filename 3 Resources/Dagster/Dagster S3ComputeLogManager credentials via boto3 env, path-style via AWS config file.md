---
title: "Dagster S3ComputeLogManager: credentials via boto3 env, path-style via AWS config file"
created: 2026-09-03
type: lesson
status: seedling
source: "leo-customer360 Phase 0 S3 logs, 2026-09-03"
tags: [dagster, s3, minio, boto3, kubernetes, gotcha]
---

# Dagster S3ComputeLogManager: credentials via boto3 env, path-style via AWS config file

Dagster's `S3ComputeLogManager` (from `dagster-aws`) sends op/run compute logs to an S3-compatible store so any webserver/daemon pod can serve them (required once you run more than one Dagster process). Two non-obvious wiring facts:

1. **Credentials are NOT in the manager config.** It builds a plain `boto3` client, so access keys come from the ambient boto3 chain — set `AWS_ACCESS_KEY_ID` / `AWS_SECRET_ACCESS_KEY` (and region) in the pod env. Map them from whatever secret holds your MinIO/vStorage keys. The config block only takes `bucket`, `prefix`, `endpoint_url`, `region`, `use_ssl`, `skip_empty_files`, etc.
2. **You cannot set boto3 addressing style in the manager config.** MinIO / most S3-compatible stores need **path-style** addressing, but `S3ComputeLogManager` exposes no `addressing_style`. Force it globally via an AWS config file (`~/.aws/config` or a baked file pointed at by `AWS_CONFIG_FILE`):
   ```
   [default]
   s3 =
       addressing_style = path
   ```
   App code that builds its own boto3 client with an explicit `Config(s3={"addressing_style": ...})` is unaffected — an explicit client Config overrides the config-file default.

Also: the target **bucket must already exist** (the manager does not create it), and use a dedicated `prefix` if you reuse an existing bucket.

## Related
[[Dagster auto-creates its tables but not the database]]
[[Scaling Dagster needs Postgres storage and a singleton daemon before adding replicas]]

## Related

- [[Dagster auto-creates its tables but not the database]]
