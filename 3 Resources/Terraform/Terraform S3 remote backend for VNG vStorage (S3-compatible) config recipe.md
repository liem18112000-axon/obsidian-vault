---
title: "Terraform S3 remote backend for VNG vStorage (S3-compatible) config recipe"
created: 2026-08-20
type: howto
status: seedling
source: "session 2026-08-20, deployments backend.tf"
tags: [terraform, s3-backend, vngcloud, vstorage, remote-state, howto]
---

# Terraform S3 remote backend for VNG vStorage (S3-compatible) config recipe

VNG Cloud vStorage is S3-compatible, so Terraform state can live in it via the standard `s3` backend pointed at the vStorage endpoint — but you must disable every AWS-only preflight, same as the aws provider does for buckets. Working backend block (Terraform >= 1.6):

```hcl
terraform {
  backend "s3" {
    bucket = "leocdp360-tfstate"
    key    = "server/terraform.tfstate"   # per module
    region = "us-east-1"                    # vStorage REQUIRES us-east-1 (SDK omits region in the URL only for this)
    endpoints = { s3 = "https://hcm04.vstorage.vngcloud.vn" }   # attribute (=), not a block
    use_path_style              = true      # renamed from force_path_style in the modern s3 backend
    skip_credentials_validation = true
    skip_metadata_api_check     = true
    skip_region_validation      = true
    skip_requesting_account_id  = true
    skip_s3_checksum            = true      # vStorage rejects the AWS default (CRC) checksum
    workspace_key_prefix        = "env"     # workspaces -> env/<workspace>/<key>
  }
}
```

Key gotchas: (1) backend blocks cant use variables, so bucket/endpoint/region are literals (non-secret); credentials come from env `AWS_ACCESS_KEY_ID`/`AWS_SECRET_ACCESS_KEY`, never the block. (2) `endpoints = { s3 = ... }` is a nested ATTRIBUTE (assign with =), not a block, in the modern s3 backend (the old top-level `endpoint =` is deprecated). (3) `skip_s3_checksum = true` is essential — without it non-AWS S3 stores 400/501 on the checksum. (4) No DynamoDB on vStorage → no state locking. Migrate existing local state with `terraform -chdir=<m> init -migrate-state -force-copy` (AWS_* creds exported). Used to make leo-customer360 CD read state on GitHub runners. Related: [[CI-driven CD cannot resolve local gitignored Terraform state — needs remote backend or IPs via secrets]].

## Related

- [[CI-driven CD cannot resolve local gitignored Terraform state — needs remote backend or IPs via secrets]]

## State locking without DynamoDB (follow-up)

The "no DynamoDB on vStorage → no state locking" gap has a fix: on **Terraform ≥ 1.10** add `use_lockfile = true` to the `backend "s3"` block for **S3-native locking** (a lock object written via conditional PUT / `If-None-Match`) — no DynamoDB needed. Verify the S3-compatible store honours conditional PUT (vStorage should; test it). Without a lock, the `-lock-timeout` flag deploy scripts pass is a **no-op** and concurrent applies (local + CI) can corrupt state. Surfaced in the 2026-08-21 `deployments/` code review (finding M4).

**Correction (verified 2026-08-21):** vStorage does **NOT** enforce `If-None-Match` — a probe's second `PutObject` with `IfNoneMatch:*` **succeeded** instead of returning `412 PreconditionFailed`. So `use_lockfile` would give **false safety** on vStorage; native S3 state locking is **not achievable** there. Mitigate operationally instead: serialise via CI `concurrency` (single writer) and never run concurrent applies. Only enable `use_lockfile` against a store that genuinely enforces conditional PUT (real AWS S3, MinIO, etc.). Test with a two-PUT `IfNoneMatch:*` probe before trusting it.
