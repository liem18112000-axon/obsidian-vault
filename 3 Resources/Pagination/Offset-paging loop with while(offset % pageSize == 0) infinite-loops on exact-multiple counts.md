---
title: "Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple counts"
created: 2026-06-21
type: lesson
status: seedling
source: "session 2026-06-21 luz-docs delete-folder review"
tags: [pagination, gotcha, infinite-loop, luz-docs, mongodb]
---

# Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple counts

Offset-based pagination loops that use the running offset in the continue-condition — `while (offset % pageSize == 0)` or `while (offset > 0 && offset % pageSize == 0)` — infinite-loop when the total matching row count is an exact non-zero multiple of pageSize.

**Trace** (pageSize=500, total=500): page1 `from=0` returns 500 rows → offset=500 → `500%500==0` true → loop. page2 `from=500` returns 0 rows → offset stays 500 → condition still true → re-fetches the same empty page forever, hammering the backend. Only the exact-multiple case triggers it; total=501 terminates normally (last short page makes offset%pageSize != 0). The bug hides until a collection happens to hit a round multiple.

**Fix**: don't derive the stop-test from the cursor. Capture the actual returned page size and loop `while (pageSize == PAGE_SIZE)` — any short OR empty page terminates. Advance the cursor by the actual returned count (`from += pageSize`), never by a consumed/filtered element count, or the cursor drifts and skips/re-reads rows.

Found in luz_docs `FolderUtil` (`forEachDocumentPage` + `getSubFolders`), folder-delete discovery phase — ironic because the feature exists to stop large-folder deletes from hanging.

## Related
[[JsonValue.NULL is a non-null Java object so Objects::nonNull does not drop JSON null elements]]

## Related

- [[JsonValue.NULL is a non-null Java object so Objects::nonNull does not drop JSON null elements]]
