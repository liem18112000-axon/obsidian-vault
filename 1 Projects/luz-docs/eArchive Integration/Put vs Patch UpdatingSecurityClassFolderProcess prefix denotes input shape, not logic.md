---
title: "Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic"
created: 2026-06-04
type: observation
status: seedling
source: "session 2026-06-04, FolderService.java"
tags: [luz-docs, design-rationale, folder-recovery, LUZ-155136]
---

# Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic

In luz_docs, the `Put`/`Patch` prefixes on `UpdatingSecurityClassFolderProcess` subclasses describe the **input shape of the triggering API style**, not different recompute logic — both share the same subtree security-class recompute in the parent class.

- **Put variant**: takes two full metadata documents (before/after) and triggers when `parentFolderIds` differs between them.
- **Patch variant**: takes a JSON Patch operations array; it is a *thin adapter* — it scans the ops for a `parentFolderIds` touch, re-fetches the updated metadata from Mongo (extra DB read), then constructs and delegates to the Put variant.

Folder recovery (LUZ-155136 fix in `FolderService`) calls the **Put** variant directly because the recovery flow already holds both full documents in memory. Routing through Patch would mean fabricating fake patch ops plus an extra Mongo read, only to arrive at the same Put code path. Recovery semantically *is* a PUT: it replaces the whole `parentFolderIds` value with a known final list.

## Related
- [[Pick the variant matching the data you already hold, not the triggering operation]]
