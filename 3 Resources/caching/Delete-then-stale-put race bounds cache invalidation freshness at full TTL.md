---
title: "Delete-then-stale-put race bounds cache invalidation freshness at full TTL"
created: 2026-07-23
type: lesson
status: seedling
source: "session 2026-07-23 MaterializeGate stampede panel"
tags: [cache, invalidation, race-condition, distributed-systems]
---

# Delete-then-stale-put race bounds cache invalidation freshness at full TTL

Event-driven cache invalidation (delete the key when the source of truth changes) has an inherent race when the cache offers only get/put/delete (no CAS/versioning): a recompute that **read the old state before the change** can land its `put` **after** the invalidating `delete`. The event is consumed, nothing retries, and the stale value lives for its *full TTL* — so the worst-case staleness bound of "invalidation" is not ~0, it is the TTL itself. Probability is amplified exactly when it matters: many recomputes are in flight during a cold-burst window, so a state change landing inside one nearly guarantees the stale overwrite.

Mitigations (compose):
- **Write-through instead of bare delete** — the event handler writes the *new* correct value; a racing stale put can still overwrite it, but the window shrinks from "until TTL" to "until next recompute".
- **Delayed re-invalidate** — schedule one best-effort second delete ~60s after the event; catches the vast majority of stale-put-after-delete orderings for ~3 lines.
- **Short TTL for imminent-transition reads** — see [[Raising negative-cache TTL turns transient failures into long-lived poison]]; the stale put then carries a short fuse.
- **Log the delete result** — a swallowed invalidation failure (safeCache-style guard) means TTL-length staleness with zero trace; invalidate helpers must log failures internally, not rely on caller-level catch blocks that can never fire.

Also: hook **every** state-transition writer, not just the happy path — a forgotten demotion/rollback site (e.g. an idle-event orchestrator resetting crashed campaigns) leaves the unsafe direction uncovered while the design claims "all transitions event-hooked".

## Related

- [[Raising negative-cache TTL turns transient failures into long-lived poison]]
