---
ai_hash: 3420ba9614ef2f57
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities:
- luz_docs parent-change cascade
- setEquals slot-differs expr
- 207 diagnostic
- 'finding #25'
- eArchive materialise code review
- sprint-158
- updateManyByFilter
- loose filter
- _folderIds
- affectedFolderIds
- HTTP 207
- real partial write
- benign 207 swallow
- tight folder-rename filter
- $in
- $expr
- $anyElementTrue
- $map
- range(0, size(_folderIds))
- $indexOfArray
- _folderSecurityClassCodes[i]
- set-inequality
- MaterializeCascadeException
- '@Retry'
- '@Fallback'
- snapshot
- MaterializeMultiStatusException
- repo unit test
- folder collection
- computed-unions map keys
- Materialize folder parentFolderIds change cascade (LUZ-154159)
- Tight updateMany filter makes HTTP 207 a reliable partial-write signal
- Materialize code review report - sprint-156 findings index
- Option A
source: 'luz_docs #25 sprint-158 fix-existing-bugs'
status: seedling
tags:
- luz-docs
- materialize
- earchive
- mongodb
- cascade
- LUZ-154159
title: luz_docs parent-change cascade tightened with setEquals slot-differs expr to
  make 207 diagnostic
type: lesson
---

# luz_docs parent-change cascade tightened with setEquals slot-differs expr to make 207 diagnostic

Fix for finding #25 (eArchive materialise code review, sprint-158). The parent-change cascade re-stamps doc sentinels via a deterministic `updateManyByFilter` whose filter was **loose**: `{_folderIds:{$in: affectedFolderIds}}`. That matched every doc touching an affected folder, including ones already carrying the correct codes, so HTTP 207 was expected on success yet identical to a real partial write — the code's blanket 'benign 207' swallow was unsound. (Background: [[3 Resources/Data/MongoDB/Tight updateMany filter makes HTTP 207 a reliable partial-write signal]].)

**Option A applied:** tighten the filter to mirror the existing tight folder-rename filter. Keep `_folderIds $in` (index-friendly prefilter) **and** add a `$expr`:

- `$anyElementTrue` over a `$map` of the doc's folder positions `range(0, size(_folderIds))`;
- per position: `$indexOfArray(affectedIdsLiteral, _folderIds[i])` → if >= 0, the folder is affected, so test whether its existing `_folderSecurityClassCodes[i]` slot is **not** set-equal to that folder's freshly-computed (own ∪ inherited) union;
- set-inequality encoded as `{$eq:[{$setEquals:[slot, union]}, false]}`.

Result: only docs that genuinely need re-stamping match ⇒ matched==modified on success ⇒ any 207 = real partial write, wrapped as retryable `MaterializeCascadeException` (`@Retry` re-stamps still-stale docs, `@Fallback` rolls back via snapshot). Deleted the now-dead `MaterializeMultiStatusException` + its benign catch.

**Secondary effect:** `$in` is built from the computed-unions **map keys**, so it narrows to folders that actually exist / have a computable union. A folder absent from the folder collection has no union to stamp; the `$expr` still catches its docs via their other affected folders. Required updating a repo unit test that had mocked the folder fetch to return empty (the $in then collapses to size 0).

## Related

- [[Materialize folder parentFolderIds change cascade (LUZ-154159)]]
- [[3 Resources/Data/MongoDB/Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
- [[Materialize code review report - sprint-156 findings index]]

%% ai-graph-start %%

**Related notes:**
- [[Tight updateMany filter makes HTTP 207 a reliable partial-write signal]]
- [[Deterministic Mongo pipeline updates return matched-not-modified; treat jsonstore SC_MULTI_STATUS as benign]]
- [[Materialize folder parentFolderIds change cascade (LUZ-154159)]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap, paging races]]

**Relations:**
- luz_docs parent-change cascade — *tightened with* — setEquals slot-differs expr
- luz_docs parent-change cascade — *improves* — 207 diagnostic
- luz_docs parent-change cascade — *fixes* — finding #25
- finding #25 — *identified in* — eArchive materialise code review
- eArchive materialise code review — *occurred in* — sprint-158
- luz_docs parent-change cascade — *uses* — updateManyByFilter
- updateManyByFilter — *had a* — loose filter
- loose filter — *used* — _folderIds
- loose filter — *used* — affectedFolderIds
- HTTP 207 — *was identical to* — real partial write
- loose filter — *caused* — HTTP 207
- benign 207 swallow — *was* — unsound
- Option A — *applied to* — updateManyByFilter
- Option A — *tightens the* — filter
- filter — *mirrors* — tight folder-rename filter
- filter — *keeps* — $in
- filter — *adds* — $expr
- $expr — *uses* — $anyElementTrue
- $anyElementTrue — *operates over* — $map
- $map — *uses* — range(0, size(_folderIds))
- $expr — *evaluates* — _folderSecurityClassCodes[i]
- $expr — *checks for* — set-inequality
- set-inequality — *encoded as* — {$eq:[{$setEquals:[slot, union]}, false]}
- real partial write — *wrapped as* — MaterializeCascadeException
- MaterializeCascadeException — *is a* — retryable exception
- @Retry — *re-stamps* — still-stale docs
- @Fallback — *rolls back via* — snapshot
- MaterializeMultiStatusException — *was* — deleted
- $in — *is built from* — computed-unions map keys
- $in — *narrows to* — folders that exist
- repo unit test — *updated due to* — Secondary effect
- repo unit test — *had mocked* — folder fetch
- luz_docs parent-change cascade — *related to* — Materialize folder parentFolderIds change cascade (LUZ-154159)
- luz_docs parent-change cascade — *related to* — Tight updateMany filter makes HTTP 207 a reliable partial-write signal
- luz_docs parent-change cascade — *related to* — Materialize code review report - sprint-156 findings index

%% ai-graph-end %%