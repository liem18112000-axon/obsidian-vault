---
title: "GreenNode is VNG Cloud's AI cloud exposing vDB and vStorage managed services"
created: 2026-08-03
aliases: ["GreenNode vDB", "GreenNode vStorage"]
type: concept
status: seedling
source: "session 2026-08-03"
tags: [greennode, vngcloud, cloud, dbaas]
---

# GreenNode is VNG Cloud's AI cloud exposing vDB and vStorage managed services

GreenNode is the AI-cloud brand of **VNG Cloud** (a Vietnamese provider). Its managed data services are exposed as two product families:

- **vDB** — managed databases: PostgreSQL, Redis (branded "Memory store"), and Kafka.
- **vStorage** — object storage, accessible via both an **S3-compatible** API and OpenStack **Swift**; regional endpoints look like `https://hcm03.vstorage.vngcloud.vn`.

Because GreenNode and VNG Cloud are the same platform, documentation is mirrored across `docs.greennode.ai` and `docs.vngcloud.vn` (docs.vngcloud.vn URLs 307-redirect to docs.greennode.ai). The IAM/console lives at `*.console.greennode.ai`.

## Related
- [[VNG Cloud Terraform provider vDB service-to-resource mapping]]
- [[LEO Customer360 GreenNode Terraform infrastructure]]
