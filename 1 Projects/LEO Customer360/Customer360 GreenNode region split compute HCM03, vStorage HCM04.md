---
title: "Customer360 GreenNode region split: compute HCM03, vStorage HCM04"
created: 2026-08-03
type: observation
status: seedling
source: "session 2026-08-03"
tags: [greennode, region, hcm03, hcm04, vstorage, customer360]
---

# Customer360 GreenNode region split: compute HCM03, vStorage HCM04

The Customer360 GreenNode deployment is **cross-region**:

- **Compute + vDB + network → HCM03** — vServer, PostgreSQL/Redis/Kafka, VPC. Console host `hcm-3.console.greennode.ai`, zone `HCM03-1A`.
- **vStorage → HCM04** — object storage, bucket `demo-leocdp`, endpoint `hcm04.vstorage.vngcloud.vn` (confirmed via a signed ListBuckets: HCM04 → 200, HCM03 → 403).

This is valid because S3 buckets are reached by endpoint, not by VPC. But cross-region S3 adds latency and possible egress cost — for high-throughput ingestion backup / Kafka DLQ mirroring in production, consider creating a vStorage bucket in HCM03 to co-locate with compute.

In `terraform/.env`: `zone_id` + `vng_vserver_base_url` + `vng_vlb_base_url` point at HCM03; `vstorage_region` + `vstorage_s3_endpoint` point at HCM04; `vng_vdb_base_url` is region-agnostic.

## Related
- [[LEO Customer360 GreenNode Terraform infrastructure]]
- [[VNG Cloud resource ID prefixes and the HCM zone_id label gotcha]]
- [[Confirm an S3-compatible object store region with a signed curl ListBuckets]]
