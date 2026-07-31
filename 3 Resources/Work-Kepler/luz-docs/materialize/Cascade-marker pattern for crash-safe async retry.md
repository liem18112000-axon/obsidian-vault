---
title: "Cascade-marker pattern for crash-safe async retry"
created: 2026-06-05
type: model
status: seedling
source: "MaterializeFolderRenameService.java 2026-06-05; materialize-gate reliability research 2026-07-15"
tags: [luz-docs, materialize, design-pattern, reliability, resilience, cdi]
---

# Cascade-marker pattern for crash-safe async retry

luz_docs' reusable shape for **durable retry of a fire-and-forget async write, with no cron/scheduler** — CDI `fireAsync` is at-most-once, so a pod death silently loses the cascade. Implemented in `ch.klara.luz.docs.materialize`: `MaterializeCascadeMarker` + `MaterializeFolderRenameService` / `MaterializeFolderParentChangeService`, swept by `MaterializeResponseFilter` (`MaterializeRequestFilter` in the rename path).

**Mechanism**
1. Write a durable Mongo marker `{cascadeType, targetId, status}` (enum `START`/`PARTIAL`) **before** the write that must survive a crash.
2. Outcomes: success → delete the marker; partial → `status=PARTIAL`; exception or hard crash (SIGKILL, pod restart) → the marker simply remains. The marker **is** the recovery state; no in-memory state is needed.
3. Retry trigger is the **next user request** — a `ContainerRequestFilter` on every allowlisted-tenant/materialize-enabled request fires the sweep. Actively-used tenants heal themselves; idle tenants cost nothing.
4. Sweep pass: read pending markers batch-limited, delete markers whose target vanished, **claim** each one (flip `PARTIAL`→`START`) so concurrent requests can't double-process it, re-fire the original event with the markerId, then delete (success) or revert to `PARTIAL`.

**Two load-bearing insights**
- The marker stores the **target id, never the diff/payload** → retry recomputes from current DB state, so a stale diff can never be replayed; with idempotent recompute, replays are harmless.
- **Crash window:** creating the marker inside the observer leaves a gap (write OK → pod dies before the async observer runs → change lost). Hardening: create the marker **synchronously in the write path before `fireAsync`**; observers only complete/delete it (+1 insert per tracked change).

**Known limitation:** no dead-letter / max-attempt cap — a permanently-failing marker retries forever, the same gap as the migration-campaign attempt counter. The pattern is nonetheless the natural template for a per-document dead-letter list, or for healing a campaign status stuck `IN_PROGRESS`.

## Related

- [[Migration campaign status can silently drift from real document state]]
- [[luz_docs migration campaigns retry on next tenant request, not cron]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
