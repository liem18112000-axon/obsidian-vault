---
title: "luz_docs parent-change cascade tightened with setEquals slot-differs expr to make 207 diagnostic"
created: 2026-06-08
type: lesson
status: seedling
source: "luz_docs #25 sprint-158 fix-existing-bugs"
tags: [luz-docs, materialize, earchive, mongodb, cascade, LUZ-154159]
---

# luz_docs parent-change cascade tightened with setEquals slot-differs expr to make 207 diagnostic

Fix for finding #25 (eArchive materialise code review, sprint-158). The parent-change cascade re-stamps doc sentinels via a deterministic `updateManyByFilter` whose filter was **loose**: `{_folderIds:{$in: affectedFolderIds}}`. That matched every doc touching an affected folder, including ones already carrying the correct codes, so HTTP 207 was expected on success yet identical to a real partial write — the code's blanket 'benign 207' swallow was unsound. (Background: [[Tight update filter makes Mongo matched-vs-modified count diagnostic]].)

**Option A applied:** tighten the filter to mirror the existing tight folder-rename filter. Keep `_folderIds $in` (index-friendly prefilter) **and** add a `$expr`:

- `$anyElementTrue` over a `$map` of the doc's folder positions `range(0, size(_folderIds))`;
- per position: `$indexOfArray(affectedIdsLiteral, _folderIds[i])` → if >= 0, the folder is affected, so test whether its existing `_folderSecurityClassCodes[i]` slot is **not** set-equal to that folder's freshly-computed (own ∪ inherited) union;
- set-inequality encoded as `{$eq:[{$setEquals:[slot, union]}, false]}`.

Result: only docs that genuinely need re-stamping match ⇒ matched==modified on success ⇒ any 207 = real partial write, wrapped as retryable `MaterializeCascadeException` (`@Retry` re-stamps still-stale docs, `@Fallback` rolls back via snapshot). Deleted the now-dead `MaterializeMultiStatusException` + its benign catch.

**Secondary effect:** `$in` is built from the computed-unions **map keys**, so it narrows to folders that actually exist / have a computable union. A folder absent from the folder collection has no union to stamp; the `$expr` still catches its docs via their other affected folders. Required updating a repo unit test that had mocked the folder fetch to return empty (the $in then collapses to size 0).

## Related

- [[Materialize folder parentFolderIds change cascade (LUZ-154159)]]
- [[Tight update filter makes Mongo matched-vs-modified count diagnostic]]
- [[Materialize code review report - sprint-156 findings index]]
