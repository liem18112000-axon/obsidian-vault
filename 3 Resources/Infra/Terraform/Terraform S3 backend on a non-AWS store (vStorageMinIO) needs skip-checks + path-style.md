---
title: "Terraform S3 backend on a non-AWS store (vStorage/MinIO) needs skip-checks + path-style"
created: 2026-06-24
type: howto
status: seedling
source: "appsflyer-data-connector IaC appendix, 2026-06-24"
tags: [terraform, backend, s3, vstorage, vngcloud, minio, state]
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
