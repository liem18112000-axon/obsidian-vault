---
title: "Cascade marker pattern for bulk update resilience"
created: 2026-07-14
type: pattern
status: seedling
tags: [resilience, bulk-update, mongodb, cascade, checkpoint]
---

# Cascade marker pattern for bulk update resilience

A bulk cascade that touches thousands of documents cannot be atomic, so give it an explicit **checkpoint document**: write a marker before the first batch, advance it per batch, delete it on completion. A crash at doc 5 000 of 10 000 then leaves a marker that says exactly where to resume — instead of a silent partial update that either never finishes or gets re-run from zero.

Lifecycle (`MaterializeRepository`, folder rename / parent-change cascades):
- `startCascadeMarker(docId, operation)` — create marker, state pending
- `claimCascadeMarker` — a worker claims a pending marker and reserves a batch (batched with explicit limits so no single update times out)
- `markCascadeMarkerPartial` — batch done, next batch pending
- `deleteCascadeMarker` — cascade complete
- a background job polls for **stale** markers (claimed but not advanced) and retries or escalates

**Preconditions:** the cascade step must be idempotent (a resumed batch may redo work), and you need job infrastructure to sweep stale markers — without the sweeper the marker only records the damage, it doesn't repair it.

**Gotchas:** index the marker lookup (`_cascade_marker_status`, `_timestamp`) or the sweep becomes the bottleneck; skip markers entirely for bulk ops that are fast and atomic anyway; decide up front whether markers are deleted on completion or retained for audit/postmortem.

## Related

- [[Sentinel fields pattern for query optimization]]
- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
