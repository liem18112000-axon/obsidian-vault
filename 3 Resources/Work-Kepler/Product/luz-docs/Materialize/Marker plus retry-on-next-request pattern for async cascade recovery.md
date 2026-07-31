---
title: "Marker plus retry-on-next-request pattern for async cascade recovery"
created: 2026-06-05
type: model
status: seedling
source: "session 2026-06-05, MaterializeFolderRenameService.java"
tags: [luz-docs, materialize, resilience, cdi, pattern]
---

# Marker plus retry-on-next-request pattern for async cascade recovery

Recovery pattern for fire-and-forget async cascades (CDI `fireAsync` is at-most-once) without adding a scheduler — as implemented by luz_docs `MaterializeFolderRenameService` + `MaterializeRequestFilter`:

1. Observer inserts a marker row `{cascadeType, targetId, status=START}` before doing cascade work; on retry an existing markerId is passed in and reused.
2. Outcomes: complete → delete marker; partial → `status=PARTIAL`; exception/pod death → marker just remains.
3. Retry trigger is the NEXT user request: a `ContainerRequestFilter` on every allowlisted-tenant request fires a retry event — an actively-used tenant heals itself, idle tenants cost nothing.
4. Retry pass: read pending markers batch-limited, delete markers whose target vanished, CLAIM each (prevents concurrent requests double-picking), re-fire the original event with the markerId.

Two key insights:
- The marker stores the TARGET id, never the diff/payload → retry recomputes from current DB state, so a stale diff can never be replayed; combined with idempotent recompute, replays are harmless.
- Crash window: marker created inside the observer leaves a gap (write OK → pod dies before async observer runs → change lost). Hardening: create the marker synchronously in the write path before `fireAsync`; observers only complete/delete it (+1 insert per tracked change).

Adopted for [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]] (design doc §4.1).

## Related

- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
