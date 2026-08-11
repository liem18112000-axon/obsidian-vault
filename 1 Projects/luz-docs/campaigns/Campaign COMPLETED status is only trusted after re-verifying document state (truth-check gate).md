---
ai_hash: 2d22f3f5bc78a17d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- Campaign COMPLETED status
- truth-check gate
- migration campaign
- MongoDB document state
- MaterializeRepository.isMaterialized
- ParallelizeRepository.isSharded
- INCOMPLETE status
- daily cycle
- LUZ-157705
- ContainerResponseFilter
- MaterializeRequestFilter
- ParallelizeRequestFilter
- CDI event
- fireAsync
- NotificationOptions.ofExecutor
- tenantId
- token
- '*CampaignCheckService'
- DualCache
- '*_campaign_check marker key'
- TTL constant (3600s)
- campaign read
- Mongo count
- updateStatus(INCOMPLETE) operation
- marker
- TTL (86400s)
- MigrationEventTrigger
- Orchestrator cycle
- commit 70d97676f
- MaterializeGate
- ParallelizeGate
- hot read path
- async filter+event pair
- async checker
- Async CDI observers
- session token
- event payload
- luz-docs migration campaign framework
- Async CDI observers must receive the session token via the event payload (note)
- check
- flip
- check order
- non-COMPLETED campaign
- violation
- healthy path
- not-COMPLETED path
- Rejected earlier shape
source: session 2026-07-21, LUZ-157705
status: seedling
tags:
- luz-docs
- migration-campaign
- materialize
- parallelize
- self-healing
- cache
- cdi-events
title: Campaign COMPLETED status is only trusted after re-verifying document state
  (truth-check gate)
type: model
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

%% ai-graph-start %%

**Related notes:**
- [[Async CDI observers must receive the session token via the event payload]]
- [[Migration campaign status can silently drift from real document state]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]
- [[luz-docs migration campaign per-tenant activation flow]]
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]

**Relations:**
- Campaign COMPLETED status — *is trusted after* — truth-check gate
- truth-check gate — *re-verifies* — MongoDB document state
- migration campaign — *has* — Campaign COMPLETED status
- MongoDB document state — *is checked by* — MaterializeRepository.isMaterialized
- MongoDB document state — *is checked by* — ParallelizeRepository.isSharded
- migration campaign — *is flipped to* — INCOMPLETE status
- INCOMPLETE status — *is healed by* — daily cycle
- LUZ-157705 — *defines* — Final shape
- ContainerResponseFilter — *fires* — CDI event
- ContainerResponseFilter — *uses* — fireAsync
- ContainerResponseFilter — *uses* — NotificationOptions.ofExecutor
- MaterializeRequestFilter — *is a type of* — ContainerResponseFilter
- ParallelizeRequestFilter — *is a type of* — ContainerResponseFilter
- CDI event — *carries* — tenantId
- CDI event — *carries* — token
- *CampaignCheckService — *observes* — CDI event
- *CampaignCheckService — *is gated by* — DualCache
- DualCache — *uses* — *_campaign_check marker key
- *_campaign_check marker key — *has* — TTL constant (3600s)
- check order — *starts with* — campaign read
- check order — *continues with* — Mongo count
- non-COMPLETED campaign — *skips* — Mongo count
- updateStatus(INCOMPLETE) operation — *is triggered on* — violation
- marker — *is written with* — TTL (86400s)
- flipped tenant — *is healed by* — MigrationEventTrigger
- MigrationEventTrigger — *triggers* — Orchestrator cycle
- commit 70d97676f — *defines* — final semantics for marker
- healthy path — *does not write* — marker
- not-COMPLETED path — *does not write* — marker
- check — *retries on failure on* — next request
- flip — *retries on failure on* — next request
- Rejected earlier shape — *involved* — MaterializeGate
- Rejected earlier shape — *involved* — ParallelizeGate
- Rejected earlier shape — *put* — Mongo count
- Mongo count — *was in* — hot read path
- async filter+event pair — *keeps* — hot read path untouched
- MaterializeGate — *trusts* — Campaign COMPLETED status
- ParallelizeGate — *trusts* — Campaign COMPLETED status
- async checker — *invalidates* — Campaign COMPLETED status
- Async CDI observers — *must receive* — session token
- session token — *is received via* — event payload
- luz-docs migration campaign framework — *is related to* — migration campaign
- Async CDI observers must receive the session token via the event payload (note) — *explains why* — token

%% ai-graph-end %%