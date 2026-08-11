---
ai_hash: ab2c41d43c4a2276
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities: []
source: appsflyer-data-connector VNG deployment report, 2026-06-24
status: seedling
tags:
- vngcloud
- vstorage
- s3
- minio
- object-storage
- deployment
- airflow
- vks
title: VNG Cloud vStorage is a drop-in S3 backend for path-style S3 clients (MinIO
  swap)
type: howto
---

# VNG Cloud vStorage is a drop-in S3 backend for path-style S3 clients (MinIO swap)

VNG Cloud **vStorage Object Storage** is S3-compatible (S3 API + OpenStack Swift), with a region-prefixed endpoint of the form \`https://<region>.vstorage.vngcloud.vn\` (confirmed for HAN02: \`https://han02.vstorage.vngcloud.vn\`).

**Drop-in for MinIO when the client already does path-style + configurable endpoint.** The appsflyer-data-connector points Spark/boto3 at MinIO via \`fs.s3a.path.style.access=true\` + a configurable \`fs.s3a.endpoint\` (and boto3 path-style in \`common/s3.py\`). Because vStorage is just another S3 endpoint, switching from MinIO to vStorage is **pure config** — set the \`MINIO_ENDPOINT\`/key/secret/bucket env vars to the vStorage values, no code change. Path-style matters because many non-AWS S3 services do not support virtual-host-style bucket addressing.

**VNG service mapping for a containerized data pipeline:** images → vContainer Registry (VCR); object storage → vStorage; Docker-daemon compute → vServer (VM); managed Kubernetes → VKS; managed Postgres → vDB; load balancer → VLB.

**Gotcha — DockerOperator/docker-out-of-docker does not move to VKS for free.** An Airflow DAG built on \`DockerOperator\` + host bind mounts assumes a real Docker daemon and host filesystem, so it deploys cleanly on a vServer VM but needs refactoring to \`KubernetesPodOperator\` (and an image-baked job script instead of a bind mount) to run on VKS. That is a code change, not a config change — a deployment-platform decision to make early.

See [[Airflow env-var Variables backend uppercases the key (AIRFLOW_VAR_KEY)]].

## Related

- [[Airflow env-var Variables backend uppercases the key (AIRFLOW_VAR_KEY)]]

%% ai-graph-start %%

**Related notes:**
- [[Terraform S3 backend on a non-AWS store (vStorageMinIO) needs skip-checks + path-style]]
- [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]
- [[VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources]]

%% ai-graph-end %%