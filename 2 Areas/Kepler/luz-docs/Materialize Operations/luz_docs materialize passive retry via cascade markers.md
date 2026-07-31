---
ai_hash: 70cac3a753a6e9fb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 LUZ-154804
status: seedling
tags:
- luz-docs
- materialize
- retry
- eventual-consistency
title: luz_docs materialize passive retry via cascade markers
type: model
---

# luz_docs materialize passive retry via cascade markers

luz_docs heals incomplete materialize cascades without cron jobs or message queues: a per-tenant `materializeCascade` collection holds one marker per unfinished cascade, and the retry is *passive* — `MaterializeRequestFilter` fires a `MaterializeRetryEvent` on every request for an allowlisted tenant, so normal traffic drives the drain.

Marker lifecycle:
- `status: START` = cascade in flight (or claimed by a retry) — excluded from the retry scan to avoid races.
- `status: PARTIAL` = needs retry (e.g. jsonstore returned 207, or per-doc failures).
- Retry (`onCascadeRetry`) drains up to `CASCADE_RETRY_BATCH` (=1) PARTIAL markers per request: claim (→START) → re-run cascade → delete on success / back to PARTIAL on failure.
- A cascade that *aborts* leaves the marker in START — by design it is not retried (known limitation).

Two marker kinds share the collection, discriminated purely by the existence of `documentIds` (no type column): folder-rename markers (`folderId` only) and document-rematerialize markers storing the failed `documentIds` from a parents-change cascade (`onFolderParentsChange`). Each kind has **its own event + observer** — `MaterializeRetryEvent` → `MaterializeFolderRenameService.onCascadeRetry` and `MaterializeDocumentRetryEvent` → `MaterializeCascadeService.onDocumentRematerializeRetry` — both fired by the request filter; the reads filter by `documentIds $exists` so neither retry can touch (or delete) the other's markers. The doc marker replaced fail-loudly-with-500: the folder update returns 2xx and the failed documents converge via retry — same eventual-consistency model as the rename cascade, chosen over compensating rollback of `inheritedSecurityClassCodes`.

Related: [[Absent discriminator field as legacy default in evolving document schemas]]

## Related

- [[Absent discriminator field as legacy default in evolving document schemas]]

%% ai-graph-start %%

**Related notes:**
- [[Cascade-marker pattern for crash-safe async retry]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap, paging races]]
- [[07 Operational Notes]]
- [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]

%% ai-graph-end %%