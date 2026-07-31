---
title: "VNG Cloud IaC = Terraform provider (no first-party CLI); vStorage/registry via S3+docker"
created: 2026-06-24
type: reference
status: seedling
source: "appsflyer-data-connector deploy research, 2026-06-24"
tags: [vngcloud, terraform, iac, cli, vserver, vks, ci-cd, deployment]
---

# VNG Cloud IaC = Terraform provider (no first-party CLI); vStorage/registry via S3+docker

VNG Cloud has **no first-party general-purpose CLI** (nothing equivalent to `aws`/`gcloud`). The supported Infrastructure-as-Code / automation surface is the **official Terraform provider `vngcloud/vngcloud`** (Terraform Registry + github.com/vngcloud/terraform-provider-vngcloud).

**Auth:** an OAuth2 **service account** — `client_id` + `client_secret`, plus `token_url` and per-service base URLs (`vserver_base_url`, `vlb_base_url`, `vdb_base_url`). In CI, store client_id/secret as pipeline secrets.

**First-class Terraform resources:** vServer compute (`vngcloud_vserver_server` + network/subnet/secgroup), **VKS** Kubernetes (`vngcloud_vks_cluster` + node group, kept as separate resources so node groups can change without recreating the cluster), **VLB** load balancer, **vDB**.

**Not (clearly) in the native provider — use other surfaces:**
- **vStorage object storage** is S3-compatible → create buckets/objects with AWS CLI / s3cmd / the Terraform `aws` provider pointed at the vStorage endpoint. The S3 access/secret key (service account) is minted in the console/IAM.
- **vContainer Registry** namespace creation is console/API; pushing images is plain `docker login && docker push`.
- vServer is OpenStack-based, so the OpenStack API/REST API is a fallback for anything not yet in the provider.

**Practical Git workflow:** Terraform provisions VM+network (and VKS); an S3/CLI step makes the bucket; the app pipeline builds+pushes the image then SSH-deploys (VM) or `kubectl apply`s (VKS). Non-VNG app secrets (API tokens) are CI secrets, never provisioned.

See [[VNG Cloud vStorage is a drop-in S3 backend for path-style S3 clients (MinIO swap)]].

## Related

- [[VNG Cloud vStorage is a drop-in S3 backend for path-style S3 clients (MinIO swap)]]
