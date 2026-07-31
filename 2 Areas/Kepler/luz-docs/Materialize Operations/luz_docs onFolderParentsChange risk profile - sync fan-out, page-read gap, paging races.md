---
ai_hash: 36e3acad377e9aa6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05 LUZ-154804 code review
status: seedling
tags:
- luz-docs
- materialize
- risk
- performance
- paging
title: luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap,
  paging races
type: observation
---

# luz_docs onFolderParentsChange risk profile - sync fan-out, page-read gap, paging races

Risk review of the synchronous parents-change materialize cascade (`MaterializeCascadeService.onFolderParentsChange`), ranked:

1. **Unbounded in-request work** — per-doc `persistMaterializedFields` HTTP calls (no bulk write) over the whole subtree, inside the PATCH request thread; big subtrees = minutes, client timeouts, worker-pool exhaustion. Bulk folder PATCH multiplies it per entry. Rename cascade avoided this with async event + one updateManyByFilter.
2. **Page-read failure gap** — per-doc failures get a PARTIAL marker + passive retry, but a `findDocumentsInFolders` exception mid-pagination propagates out with NO marker: folder metadata already persisted, remaining subtree silently stale, nothing heals it. The retry only covers the caught failure mode.
3. **Skip-paging races** — `from += PAGE_SIZE` is stable vs the cascade's own writes ($set never touches folderIds) but NOT vs concurrent deletes/moves: docs sliding back across page boundaries are skipped silently; `seen` only dedups repeats.
4. **Worst-case failure shape** — jsonstore down => failed list = whole subtree: megabyte log line, giant marker, retry re-runs everything in one observer pass; no retry cap/backoff/poison-marker parking (PARTIAL<->START forever).
5. **Token mixing on retry** — markers store no principal; the retry runs under whichever user's request triggered the filter (same as rename retry, but doc writes have more security surface).

Cheap fixes: marker-on-page-read-failure (needs folder-ids marker fallback), cap failedIds in logs, retry counter to park poison markers. Architectural fix for #1: same async-marker pattern as rename.

Related: [[luz_docs materialize passive retry via cascade markers]], [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]

## Related

- [[luz_docs materialize passive retry via cascade markers]]
- [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs bulk folder PATCH runs the materialize cascade once per entry]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
- [[luz_docs materialize passive retry via cascade markers]]
- [[luz_docs parent-change cascade pipeline rebuilds _folderSecurityClassCodes positionally then re-derives the sentinels]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]

%% ai-graph-end %%