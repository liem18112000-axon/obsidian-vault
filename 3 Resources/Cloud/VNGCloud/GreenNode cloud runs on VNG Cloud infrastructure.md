---
ai_hash: ecd0fc4bd8d429bd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-26
entities: []
source: session 2026-07-26
status: seedling
tags:
- vngcloud
- greennode
- cloud
- terraform
title: GreenNode cloud runs on VNG Cloud infrastructure
type: concept
---

# GreenNode cloud runs on VNG Cloud infrastructure

GreenNode (console at dashboard.console.greennode.ai, an AI/GPU sovereign cloud) is built on VNG Cloud infrastructure. Its product surface and docs mirror VNG Cloud one-to-one: vServer (compute VMs), vDB (managed databases: RDS relational + MemoryStore Redis), VKS (Kubernetes), vStorage (S3-compatible object storage).

Practical consequence: to manage GreenNode resources as code, use the VNG Cloud Terraform provider `vngcloud/vngcloud`, not a GreenNode-specific one (none exists). Confirmed by identical doc trees: docs.greennode.ai/.../vdb/relational-database-service-rds/... == docs.vngcloud.vn/.../vdb/relational-database-service-rds/...

Note: this is distinct from Viettel IDC cloud, which is OpenStack- or vCloud-based and has its own separate providers.

## Related

- [[VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources]]

%% ai-graph-start %%

**Related notes:**
- [[VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources]]
- [[VNG Cloud IaC = Terraform provider (no first-party CLI); vStorageregistry via S3+docker]]

%% ai-graph-end %%