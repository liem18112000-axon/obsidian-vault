---
title: "S3-compatible CreateBucket InvalidLocationConstraint: set region us-east-1 to omit LocationConstraint"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18 (live test)"
tags: [s3, aws-provider, terraform, vngcloud, minio, gotcha]
---

# S3-compatible CreateBucket InvalidLocationConstraint: set region us-east-1 to omit LocationConstraint

When creating a bucket on an **S3-compatible** store (VNG vStorage, MinIO, Ceph RadosGW, etc.) with the AWS SDK / Terraform `aws_s3_bucket`, a non-`us-east-1` provider region causes:

```
InvalidLocationConstraint: The specified location-constraint is not valid   (HTTP 400 on CreateBucket)
```

**Why:** the AWS SDK sends `CreateBucketConfiguration/LocationConstraint = <region>` for every region EXCEPT `us-east-1` (its legacy default), for which it OMITS the constraint entirely. S3-compatible backends only accept their own zonegroup name (or an empty constraint), so a value like `hcm04` is rejected.

**Fix:** set the provider **`region = "us-east-1"`** so no LocationConstraint is sent. The real region is chosen by the **endpoint host** (e.g. `hcm04.vstorage.vngcloud.vn`), not by the signing region — vStorage accepts the us-east-1 SigV4 signing label fine. Verified live: with region=us-east-1 a CreateBucket against the hcm04 endpoint succeeds; with region=hcm04 it 400s.

Applies alongside the other S3-compat provider flags: `s3_use_path_style=true`, `skip_credentials_validation`, `skip_region_validation`, `skip_requesting_account_id`, `skip_metadata_api_check`, and a custom `endpoints{ s3 = ... }`.

Related: [[Manage VNG Cloud vStorage buckets with the AWS Terraform provider, not vngcloud]], [[Creating a vStorage bucket is free (data-plane); only the project quota + usage cost money]].

## Related

- [[Manage VNG Cloud vStorage buckets with the AWS Terraform provider]]
- [[not vngcloud]]
