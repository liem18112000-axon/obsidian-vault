---
title: "GreenNode cloud runs on VNG Cloud infrastructure"
created: 2026-07-26
type: concept
status: seedling
source: "session 2026-07-26"
tags: [vngcloud, greennode, cloud, terraform]
---

# GreenNode cloud runs on VNG Cloud infrastructure

GreenNode (console at dashboard.console.greennode.ai, an AI/GPU sovereign cloud) is built on VNG Cloud infrastructure. Its product surface and docs mirror VNG Cloud one-to-one: vServer (compute VMs), vDB (managed databases: RDS relational + MemoryStore Redis), VKS (Kubernetes), vStorage (S3-compatible object storage).

Practical consequence: to manage GreenNode resources as code, use the VNG Cloud Terraform provider `vngcloud/vngcloud`, not a GreenNode-specific one (none exists). Confirmed by identical doc trees: docs.greennode.ai/.../vdb/relational-database-service-rds/... == docs.vngcloud.vn/.../vdb/relational-database-service-rds/...

Note: this is distinct from Viettel IDC cloud, which is OpenStack- or vCloud-based and has its own separate providers.

## Related

- [[VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources]]
