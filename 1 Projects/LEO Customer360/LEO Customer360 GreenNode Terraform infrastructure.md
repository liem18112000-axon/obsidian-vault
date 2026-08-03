---
title: "LEO Customer360 GreenNode Terraform infrastructure"
created: 2026-08-03
type: howto
status: seedling
source: "session 2026-08-03"
tags: [terraform, greennode, vngcloud, customer360, multitenancy]
---

# LEO Customer360 GreenNode Terraform infrastructure

A Terraform project under `leo-customer360/terraform/` provisions the four managed data services the Customer360 CDP needs on **GreenNode (VNG Cloud)**: PostgreSQL, Redis, Kafka, and vStorage (S3). Branch: `infras/setup-redis-vstorage-kafka-postgressql`.

**Design decisions made:**
- **Hybrid multi-tenancy** — one shared cluster per service, with a per-tenant edge: shared Kafka topics keyed by `tenant_id` **plus** optional per-tenant topics + scoped SASL users, and shared vStorage buckets **plus** per-tenant buckets. Matches the app, which isolates tenants in-data (Postgres RLS), not per-tenant infra.
- **Per-environment PostgreSQL topology** — standalone single node in `dev`, HA cluster in `prod`, via a `topology` variable.
- **Networking supports both** — reference an existing VNG network/subnet by default, or create one behind `create_network`.

**Layout:** `modules/{network,postgres,redis,kafka,vstorage,db-bootstrap}` -> `stack/` composition -> `environments/{dev,prod}`. Idempotency is native to Terraform; in-database bootstrap (extensions/role/schema/seed) is a separate idempotent step in `db-bootstrap`.

## Related
- [[VNG Cloud Terraform provider vDB service-to-resource mapping]]
- [[vStorage has no Terraform resource so manage buckets via the AWS S3 provider]]
- [[Postgres Row-Level Security is bypassed by superusers so the app needs a non-superuser role]]
- [[vDB PostgreSQL supports PostGIS and pgvector plus the fuzzy-match extensions]]
