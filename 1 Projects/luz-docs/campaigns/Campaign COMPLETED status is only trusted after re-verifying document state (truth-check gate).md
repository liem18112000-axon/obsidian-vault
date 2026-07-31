---
title: "Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)"
created: 2026-07-21
type: model
status: seedling
source: "session 2026-07-21, LUZ-157705"
tags: [luz-docs, migration-campaign, materialize, parallelize, self-healing, cache, cdi-events]
---

# Campaign COMPLETED status is only trusted after re-verifying document state (truth-check gate)

A migration campaign's COMPLETED status must not be trusted blindly: the actual MongoDB document state (MaterializeRepository.isMaterialized / ParallelizeRepository.isSharded) is periodically re-verified, and on violation the campaign is flipped to INCOMPLETE so the existing daily cycle heals it.

**Final shape (LUZ-157705)** — fully async, off the read path:

- **Trigger:** a `ContainerResponseFilter` per package (MaterializeRequestFilter / ParallelizeRequestFilter) fires a CDI event (`fireAsync` + `NotificationOptions.ofExecutor`) carrying `(tenantId, token)` on every tenant request; the response is never delayed by the check.
- **Observer:** an `@ApplicationScoped` `*CampaignCheckService` observes with `@ObservesAsync`, gated by a dedicated DualCache marker key (`*_campaign_check`, own TTL constant, 3600s) — so the real check (campaign read + Mongo count) runs at most ~once/hour/tenant.
- **Check order:** campaign status first, repo count second — a non-COMPLETED campaign skips the expensive documents count entirely (requests already route to the old path).
- **On violation:** `updateStatus(INCOMPLETE)`, then the marker is written with TTL 86400s (1 day) — a flipped tenant is not re-checked while the daily MigrationEventTrigger → Orchestrator cycle heals it.
- **Marker only on flip (final semantics, commit 70d97676f):** the healthy path and the not-COMPLETED path write NO marker, so those tenants re-run the campaign read + count on every request; only a flip suppresses re-checking. **Decided 2026-07-21: keep as-is** — a healthy-path marker put (gate-style per-outcome TTL) was proposed twice and explicitly declined; the per-request check on healthy tenants is intentional. Do not re-propose without new evidence (e.g. measured Mongo load).
- **Failure semantics:** if the check or the flip throws, the marker is NOT written, so the whole check retries on the next request.

**Rejected earlier shape:** verifying inline inside the read-path gates (MaterializeGate/ParallelizeGate). It worked but put an extra Mongo count in the hot read path and coupled the checker to gate cache semantics; the async filter+event pair keeps the read path untouched. The gates still trust COMPLETED — the async checker is what invalidates it.

**Why the event must carry the Token:** see [[Async CDI observers must receive the session token via the event payload]].

## Related

- [[luz-docs migration campaign framework]]
- [[Async CDI observers must receive the session token via the event payload]]
