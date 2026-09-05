---
title: "Creating a vStorage bucket is free (data-plane); only the project quota + usage cost money"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18"
tags: [vngcloud, vstorage, s3, billing, object-storage, terraform]
---

# Creating a vStorage bucket is free (data-plane); only the project quota + usage cost money

In VNG vStorage (and S3-compatible object storage generally), **creating a bucket is a free data-plane operation** — it does NOT place a billing order or charge anything. This is fundamentally different from creating the vStorage **project**, which places a paid prepaid order (and, per the code-114 bug, charged even on failure).

What you actually pay for:
- the **project quota** (prepaid, chosen at project creation in the console) — already paid before any Terraform runs;
- **storage used** (per TB of data actually stored) and **bandwidth** (per GB) — accrue with usage.

Therefore `terraform apply` on the leo-customer360 storage module (which only creates `aws_s3_bucket` [+ optional versioning] via the S3 PutBucket API) creates **empty buckets = 0 bytes = 0 incremental cost**. The module's `estimated_*` cost variables are display-only outputs that provision nothing.

Rule of thumb: with S3-compatible clouds, bucket/object API calls are data-plane (no order); only the capacity/subscription purchase is a billing order. Related: [[vStorage create-project code 114 is account-side, not a payload bug]], [[Manage VNG Cloud vStorage buckets with the AWS Terraform provider, not vngcloud]].

## Related

- [[vStorage create-project code 114 is account-side]]
- [[not a payload bug]]
