---
title: "Customer360 CDP Kafka ingestion topic layout"
created: 2026-08-03
type: howto
status: seedling
source: "session 2026-08-03"
tags: [kafka, ingestion, cdp, multitenancy, dlq, customer360]
---

# Customer360 CDP Kafka ingestion topic layout

The Customer360 CDP Kafka ingestion layout is **shared topics keyed by tenant, plus DLQs, plus an opt-in per-tenant isolation edge** — matching the app's "shared infra + tenant_id" model. Provisioned by the `terraform/` Kafka module.

**Topics** (dev / prod partitions, replicas 3):
- `cdp.raw-events` (6/12) → `cdp_raw_events`. High-volume behavioral/transactional.
- `cdp.raw-profiles` (6/12) → `cdp_raw_profiles_stage`. Source-system + CRM/file imports.
- `cdp.raw-events.dlq` (3/6) and `cdp.raw-profiles.dlq` (3/3) — the design spec (`identity-resolution.md`) **requires a DLQ + object-storage backup**; these were the gap in the first draft. DLQ consumers also mirror raw bytes to the vStorage `ingestion`/`backups` bucket.
- `cdp.profile-resolved` — Phase 2 activation edge (emitted after CIR resolves a master profile; consumed by segmentation/personalization/notification/email). Commented out until a `data_synch` producer exists.
- `<tenant_code>.events` — opt-in per-tenant hard-isolation ingestion topic with a scoped `<tenant_code>-app` SASL user; needs a router to fold it back into `cdp_raw_events`.

**Keying decision (the one to get right):** producer key = `"<tenant_id>:<identity-hint>"` where identity-hint = first present of external_customer_id / device_id / cookie_id / session_id / hashed-email. This co-locates a tenant's data *and* keeps one visitor's event timeline ordered within a partition. `raw_profile_id` can't be the key — it's resolved server-side after ingestion. Dedup uniqueness in Postgres is `(tenant_id, source_system, event_dedup_key)`.

**Status:** Kafka is greenfield (no producers/consumers yet); topics are provisioned in advance so ingestion code can point at them later.

## Related
- [[LEO Customer360 GreenNode Terraform infrastructure]]
- [[vngcloud_vdb_kafka_topic exposes only retention, not cleanup.policy]]
