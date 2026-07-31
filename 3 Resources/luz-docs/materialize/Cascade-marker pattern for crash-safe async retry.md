---
title: "Cascade-marker pattern for crash-safe async retry"
created: 2026-07-15
type: model
status: seedling
source: "luz_docs materialize-gate reliability research, 2026-07-15"
tags: [luz-docs, design-pattern, reliability]
---

# Cascade-marker pattern for crash-safe async retry

In ch.klara.luz.docs.materialize (MaterializeCascadeMarker + MaterializeFolderRenameService / MaterializeFolderParentChangeService), a durable Mongo marker document is written BEFORE attempting a write that must survive a crash: marker has a status enum START/PARTIAL. On success, delete the marker. On failure or a hard crash (SIGKILL, pod restart), the marker is left at PARTIAL — no in-memory state is needed to recover, the marker itself is the recovery state.

A passive async sweeper — seeded by ordinary request traffic (MaterializeResponseFilter fires it on every materialize-enabled request, not a cron job) — claims one PARTIAL marker at a time by flipping it back to START (prevents two concurrent sweeps double-processing the same marker), retries the write, and either deletes the marker (success) or reverts it to PARTIAL (failure, for the next sweep to pick up).

This is the established reusable pattern in this codebase for 'durable retry of an async write that must survive a crash, with no dedicated cron/scheduler needed.' Worth reaching for this shape instead of inventing a new retry mechanism whenever a similar problem shows up — e.g. it's the natural template for a per-document dead-letter list for permanently-failing materialize documents, or for healing a migration-campaign status stuck IN_PROGRESS after a crash.

Known limitation of the pattern as currently implemented: no dead-letter / max-attempt cap — a marker that keeps failing retries forever (unbounded retry loop), same gap as the migration-campaign attempt counter.

## Related

- [[Migration campaign status can silently drift from real document state]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
