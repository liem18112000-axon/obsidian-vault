---
title: "luz_docs_import isExcludedFile misses Thumbs.db and __MACOSX (BR-04 gap)"
created: 2026-08-04
type: observation
status: seedling
source: "LUZ-158230 investigation 2026-08-04"
tags: [luz-docs-import, kepler, gotcha, zip-import]
---

# luz_docs_import isExcludedFile misses Thumbs.db and __MACOSX (BR-04 gap)

In luz_docs_import, `DocsImportAsyncService.isExcludedFile()` (DocsImportAsyncService.java:227) only excludes entries whose name `startsWith(".")`. Consequences for the spec BR-04 (ignore `__MACOSX/`, `.DS_Store`, `Thumbs.db`, dot-prefixed):

- `.DS_Store` and macOS `._*` resource forks → already skipped (dot-prefixed). OK.
- `Thumbs.db` → NOT dot-prefixed → currently IMPORTED as a document. Needs an explicit name check.
- `__MACOSX/` → every directory in the unzip reaches `createFolder` (the loop at :153-156 has no exclusion for dirs), so a macOS-authored zip currently CREATES a spurious `__MACOSX` folder in the eArchive. Needs the directory branch guarded.

So finishing BR-04 = extend isExcludedFile (Thumbs.db, under-__MACOSX) AND skip the `__MACOSX` directory in the folder-creation branch.

Related: [[Health ZIP import broken sidecar still imports; orphan sidecar is the only rejection]]

## Related

- [[Health ZIP import broken sidecar still imports; orphan sidecar is the only rejection]]
