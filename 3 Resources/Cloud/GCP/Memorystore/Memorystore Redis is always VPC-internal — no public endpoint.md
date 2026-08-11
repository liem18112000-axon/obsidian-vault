---
ai_hash: c0dcd66ee619b19e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: accesstrade deployment/ Terraform, 2026-06-14
status: seedling
tags:
- gcp
- memorystore
- redis
- terraform
- networking
- gotcha
title: Memorystore Redis is always VPC-internal — no public endpoint
type: lesson
---

# Memorystore Redis is always VPC-internal — no public endpoint

Memorystore for Redis has **no public endpoint** — an instance only attaches to a VPC and is reachable solely from inside it (Cloud Run with direct VPC egress or a Serverless VPC connector, GKE, or a GCE box on the network). This holds even if a sibling data store is public: you can give a Cloud SQL instance a public IP for laptop access, but Redis still needs a VPC. So **a VPC is mandatory whenever you provision Memorystore**, regardless of how the database is exposed.

With `connect_mode = DIRECT_PEERING` (the Memorystore default), Google automatically reserves a /29 from the `authorized_network` and VPC-peers it to the managed Redis network — **no manual IP-range reservation and no `google_service_networking_connection` are required**. That extra plumbing (a reserved `google_compute_global_address` + a servicenetworking connection) is only needed for the other mode, `PRIVATE_SERVICE_ACCESS`.

Seen while writing the `deployment/` Terraform for accesstrade_integration (project klara-nonprod, asia-southeast1): the chosen model was *public Cloud SQL + private Redis*, yet a dedicated VPC + subnet still had to be created purely so Redis had something to attach to.

## Related
[[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]
[[Mounting host gcloud ADC into a container to authenticate Vertex AI]]

## Related

- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]
- [[Mounting host gcloud ADC into a container to authenticate Vertex AI]]

%% ai-graph-start %%

**Related notes:**
- [[Cloud SQL ssl_mode replaced the require_ssl boolean in the hashicorp google provider]]
- [[Cloud Run v2 service design gotchas]]
- [[Non-WI GKE Google API auth mount a GSA key at the well-known ADC path]]
- [[Cloud SQL Postgres point-in-time recovery requires automated backups enabled]]
- [[VNG Cloud Terraform provider maps managed Postgres and Redis to vdb resources]]

%% ai-graph-end %%