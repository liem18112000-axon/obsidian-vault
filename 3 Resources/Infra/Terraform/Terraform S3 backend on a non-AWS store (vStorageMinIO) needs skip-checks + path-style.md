---
ai_hash: 39c544b990bbc5eb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-24
entities: []
source: appsflyer-data-connector IaC appendix, 2026-06-24
status: seedling
tags:
- terraform
- backend
- s3
- vstorage
- vngcloud
- minio
- state
title: Terraform S3 backend on a non-AWS store (vStorage/MinIO) needs skip-checks
  + path-style
type: howto
---

# Terraform S3 backend on a non-AWS store (vStorage/MinIO) needs skip-checks + path-style

Terraform can keep its **remote state in a VNG Cloud vStorage bucket** by using the standard `backend "s3"` — vStorage speaks the S3 protocol — but because it is **not** real AWS you must disable the AWS-specific preflight checks and force path-style addressing:

```hcl
backend "s3" {
  bucket    = "leocdp-tfstate"
  key       = "app/terraform.tfstate"
  region    = "hcm03"
  endpoints = { s3 = "https://hcm03.vstorage.vngcloud.vn" }
  skip_region_validation      = true
  skip_credentials_validation = true
  skip_metadata_api_check     = true
  skip_requesting_account_id  = true
  use_path_style              = true
}
```

Creds come from `AWS_ACCESS_KEY_ID`/`AWS_SECRET_ACCESS_KEY` env (the vStorage S3 keys). **Bootstrap gotcha:** the backend cannot create its own bucket — make it first with `aws --endpoint-url https://<region>.vstorage.vngcloud.vn s3 mb s3://<bucket>`.

Same skip-checks + path-style pattern applies to ANY non-AWS S3-compatible store (MinIO, Backblaze, etc.) used as a Terraform backend. See [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]].

## Related

- [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]]

%% ai-graph-start %%

**Related notes:**
- [[VNG Cloud vStorage is a drop-in S3 backend for path-style S3 clients (MinIO swap)]]
- [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]]
- [[AppsFlyer connector S3 config is vStorage-only - VSTORAGE_ env vars]]
- [[MinIO server creds (ROOT_USERPASSWORD) are distinct env vars from the S3 client creds (ACCESS_KEYSECRET_KEY)]]
- [[VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources]]

%% ai-graph-end %%