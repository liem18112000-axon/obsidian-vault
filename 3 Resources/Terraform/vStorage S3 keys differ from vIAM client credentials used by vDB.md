---
title: "vStorage S3 keys differ from vIAM client credentials used by vDB"
created: 2026-08-17
type: term
status: seedling
source: "session 2026-08-17"
tags: [vngcloud, vstorage, vdb, credentials, terraform]
---

# vStorage S3 keys differ from vIAM client credentials used by vDB

VNG Cloud has **two different credential types** and they are not interchangeable:

- **vStorage S3 keys** (`access_key` / `secret_key`) — used to talk to the S3 object-storage API. Created under vStorage console → IAM → Service account → **vStorage credentials → Create a S3 key**. The Secret Key is shown **only once** on creation.
- **vIAM service-account credentials** (`client_id` / `client_secret`) — used by the `vngcloud/vngcloud` Terraform provider for vServer / vDB (e.g. the managed PostgreSQL in `deployments/postgres`), authenticating against `iamapis.vngcloud.vn`.

So a Terraform module that provisions buckets needs S3 keys, while one that provisions a managed DB needs the vIAM client id/secret — don't reuse one for the other.

Related: [[Manage VNG Cloud vStorage buckets with the AWS Terraform provider, not vngcloud]].

## Related

- [[Manage VNG Cloud vStorage buckets with the AWS Terraform provider]]
- [[not vngcloud]]
