---
title: "Cascade marker pattern for bulk update resilience"
created: 2026-07-14
type: pattern
status: seedling
tags: [resilience, bulk-update, mongodb, cascade]
---

# Cascade marker pattern for bulk update resilience

Use explicit marker documents to coordinate and resume bulk updates across large collections, especially when cascade logic must touch thousands of documents.

**How it works:**
1. startCascadeMarker(docId, operation) → create a marker record; set state to pending.
2. Begin bulk update (batched, with explicit limits to avoid timeout).
3. On success, mark next batch pending or mark entire cascade complete.
4. On failure, leave marker in-progress or retryable.
5. A background job polls for stale markers, retries or escalates.

**Why it prevents orphaned updates:**
- If a write cascades to 10K documents and crashes at 5K, the marker records which docs are done and which remain.
- Resume jobs read the marker state and continue from the checkpoint, not from zero.
- No silent partial-updates or duplicate cascades.

**Real example — MaterializeRepository:**
- startCascadeMarker: initiates folder rename or parent-change cascade
- claimCascadeMarker: worker process claims a pending marker and reserves a batch
- markCascadeMarkerPartial: when batch finishes, mark next batch pending
- deleteCascadeMarker: final cleanup when cascade completes

**When to use:**
- Bulk operations that touch 100+ documents in a single transaction.
- When cascades can be resumed after restart (idempotent operations).
- When you have job infrastructure to poll and retry markers.

**Gotchas:**
- Markers themselves add overhead; don't use for bulk ops that are fast and atomic anyway.
- Ensure marker lookup is indexed (_cascade_marker_status, _timestamp) or it becomes a bottleneck.
- Decide: cleanup markers immediately or retain for audit trail? (retention helps with postmortems.)

## Related

- [[Sentinel fields pattern for query optimization]]
