---
title: "Sentinel fields pattern for query optimization"
created: 2026-07-14
type: pattern
status: seedling
tags: [denormalization, query-optimization, mongodb, materialize]
---

# Sentinel fields pattern for query optimization

Denormalize frequently-accessed read-path data into the document to eliminate runtime lookups, cross-collection joins, and permission checks at query time.

**Trade-off:** Simpler, faster reads vs. complex write-time cascade logic. The write side becomes responsible for keeping denormalized fields in sync whenever the source data changes.

**Real example — eArchive materialize:**
- _isPublic: boolean, eliminates security context evaluation
- _effectiveSecurityClassCodes: array of codes, avoids runtime access control checks
- _folderNames: denormalized path, skips N+1 folder lookups

**When this pattern works:**
- Reads are much more frequent than writes (or writes can absorb the cost).
- The denormalized field is small and computable (not a complex aggregation).
- Cascade/sync logic can be batched (use markers, retry queues, eventual consistency).
- You can tolerate brief staleness (eventual consistency is acceptable).

**Implementation considerations:**
- Decide: synchronous (update during write) or eventual (job-based sync)? Sync adds latency to writes; eventual means brief inconsistency.
- Index the denormalized fields; they're now query filters.
- Mark cascade state (pending/in-progress/failed) so operators can detect/repair stale data.
- Test at scale—what works for 1K docs may cascade poorly at 10M docs.

## Related

- [[Cascade marker pattern for bulk update resilience]]
- [[Materialize feature architecture]]
