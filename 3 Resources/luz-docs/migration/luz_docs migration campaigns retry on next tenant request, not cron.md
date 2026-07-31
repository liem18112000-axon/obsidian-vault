---
title: "luz_docs migration campaigns retry on next tenant request, not cron"
created: 2026-07-10
type: concept
status: seedling
source: "luz_docs prod migration incident investigation, 2026-07-10"
tags: [luz-docs, migration, design-decision]
---

# luz_docs migration campaigns retry on next tenant request, not cron

In luz_docs, migration campaigns (ch.klara.luz.docs.migration package) do not retry on a cron schedule — they retry opportunistically, driven by tenant HTTP traffic.

`MigrationEventTrigger` is a JAX-RS `ContainerRequestFilter` that fires a `MigrationEvent` on every authenticated REST request for a tenant, once per enabled+allowlisted `Campaign`. `MigrationEventOrchestrator.onListenMigrationTrigger` picks that up and re-queues the campaign unless its stored status is already `COMPLETED` (so `FAILED`/`INCOMPLETE`/unset all get retried).

Two caches gate how often this can actually happen, so a campaign will not retry the instant a new request arrives:
- `OVERLAPPING_GUARD` — 8h TTL, prevents two concurrent runs of the same campaign+tenant.
- `DEDUP_L1` (`MIGRATION_SUBJECT_TRIGGER_DEDUP_PATTERN`) — 24h TTL, prevents scheduling the same campaign+tenant more than roughly once per day.

Net effect: a campaign that failed/partially-failed self-heals automatically within about 24h, as long as the tenant keeps sending requests — no manual re-trigger needed. But don't expect (or alarm on) a retry showing up sooner than the 24h dedup window has elapsed, even while the stored status still reads FAILED/INCOMPLETE.

Related: [[Materialize migration per-doc failures leave no persisted failed-id record]].
