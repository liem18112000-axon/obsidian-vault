---
title: "Migration campaign status can silently drift from real document state"
created: 2026-07-15
type: lesson
status: seedling
source: "luz_docs materialize-gate reliability research, 2026-07-15"
tags: [luz-docs, reliability, gotcha, distributed-systems]
---

# Migration campaign status can silently drift from real document state

A gate that trusts an async-process-maintained status document instead of checking the real underlying state it describes will drift silently unless the status writer has a distinct FAILED/error state plus an attempt ceiling, or there's an independent periodic reconciliation check against ground truth (ideally both).

Concrete case: ch.klara.luz.docs.migration's campaign status (Campaign.Status: INCOMPLETE/COMPLETED/IN_PROGRESS, stored in Mongo collection luz_docs_migration_campaign) is read by MaterializeGate as the sole signal for whether documents have been materialized — with no cross-check against the documents collection itself. Root causes that let it drift:
- MigrationMessageReceiver.process's catch-all exception handler only logs a WARNING and leaves whatever status was last written (usually IN_PROGRESS, set right before execution) — there's no distinct FAILED state, so 'silently broken forever' looks identical to 'not started yet' until the next nightly drain's IN_PROGRESS-without-guard demotion notices it.
- No max-attempt/circuit-breaker: an `attempt` counter is incremented every run but never checked against a ceiling, so a permanently-broken tenant retries forever with no escalation.
- Per-document failures are tracked only in-memory for the duration of one run (an in-process HashSet) — never persisted, so the same document fails and gets silently retried from scratch every run, with no dead-letter and no visibility into 'these N documents are known-broken.'
- The executor's own per-run (total, failed) result is computed but only logged as a string — never persisted back onto the status document or exposed as a metric — even though 'failed > 0 alongside a COMPLETED status' would be a free, cheap drift-detection signal if it were kept.

## Related

- [[Cascade-marker pattern for crash-safe async retry]]
- [[DualCache L1 write ignores per-call TTL (uses domain default)]]
- [[luz_docs_statistic computes per-tenant unmaterializedDocuments count]]
- [[Fan-out gate and backfill filter must cover the same field set]]
