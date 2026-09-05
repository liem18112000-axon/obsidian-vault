---
title: "luz_docs_import: idempotent re-import replaces view-controller search-based file dedup"
created: 2026-08-07
type: argument
status: seedling
source: "session 2026-08-07 luz_docs_import"
tags: [luz-docs, earchive, dedup, idempotency, refactor]
---

# luz_docs_import: idempotent re-import replaces view-controller search-based file dedup

**Decision:** In luz_docs_import, the per-file dedup that called luz-docs-view-controller's `/documents/search` (matching an existing document by **name + byte size** in the target folder before creating) was removed. **Idempotent re-import** — skipping files already recorded in a previous job's history — already covers the real 'don't import the same file twice' need.

**Why:** the search-based dedup cost a **network round-trip per file** during import, and pulled in a whole chain of DTOs that existed only to parse that response: `SearchDocumentResponse`, `SearchDocumentResult`, `Files`, `Reference`, `QueryRequest`, `Query`, `Term` — plus `Archive.isSameParentFolder`, `JsonUtils.converObjectToJson`, `LuzDocsViewControllerService.searchDocument` and its REST-client binding, and `JobProgressWriter.recordSkipped(File)`. All became dead code and were deleted.

**Gotcha to remember:** the two 'skip' paths are different — `recordSkippedAll(List)` is the idempotent re-import skip (kept); `recordSkipped(File)` was the search-dedup skip (removed). Don't conflate them.

Context: service ch.klara.luz.docsimport, Java 17 / WildFly EJB, json-store backed jobs; eArchive ZIP import. Same session also removed the read-time timeout flip — see [[Read-time non-persisted state repair is an anti-pattern; use a scheduled reconciler]].

## Related

- [[Read-time non-persisted state repair is an anti-pattern; use a scheduled reconciler]]
