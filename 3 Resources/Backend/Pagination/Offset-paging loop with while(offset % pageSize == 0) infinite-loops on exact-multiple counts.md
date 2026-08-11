---
ai_hash: f1d56aeb2ac16f7a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-21
entities: []
source: session 2026-06-21 luz-docs delete-folder review
status: seedling
tags:
- pagination
- gotcha
- infinite-loop
- luz-docs
- mongodb
title: Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple
  counts
type: lesson
---

# Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple counts

Offset-based pagination loops that use the running offset in the continue-condition — `while (offset % pageSize == 0)` or `while (offset > 0 && offset % pageSize == 0)` — infinite-loop when the total matching row count is an exact non-zero multiple of pageSize.

**Trace** (pageSize=500, total=500): page1 `from=0` returns 500 rows → offset=500 → `500%500==0` true → loop. page2 `from=500` returns 0 rows → offset stays 500 → condition still true → re-fetches the same empty page forever, hammering the backend. Only the exact-multiple case triggers it; total=501 terminates normally (last short page makes offset%pageSize != 0). The bug hides until a collection happens to hit a round multiple.

**Fix**: don't derive the stop-test from the cursor. Capture the actual returned page size and loop `while (pageSize == PAGE_SIZE)` — any short OR empty page terminates. Advance the cursor by the actual returned count (`from += pageSize`), never by a consumed/filtered element count, or the cursor drifts and skips/re-reads rows.

Found in luz_docs `FolderUtil` (`forEachDocumentPage` + `getSubFolders`), folder-delete discovery phase — ironic because the feature exists to stop large-folder deletes from hanging.

## Related
[[3 Resources/Languages/Java/JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]

## Related

- [[3 Resources/Languages/Java/JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]

%% ai-graph-start %%

**Related notes:**
- [[JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]
- [[JAX-RS DefaultValue does not apply when a bean param is constructed in code]]
- [[luz-docs folder delete filter double-fetched every subfolder]]
- [[luz-jsonstore find returns 200 empty string, not [], on zero matches]]
- [[Minimal meaningful test fixture size is bounded by the real page size]]

%% ai-graph-end %%