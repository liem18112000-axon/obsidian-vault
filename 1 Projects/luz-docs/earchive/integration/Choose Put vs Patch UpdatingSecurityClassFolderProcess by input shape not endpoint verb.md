---
title: "Choose Put vs Patch UpdatingSecurityClassFolderProcess by input shape not endpoint verb"
created: 2026-06-04
type: concept
status: seedling
source: "session 2026-06-04"
tags: [luz-docs, security-class, design-decision]
---

# Choose Put vs Patch UpdatingSecurityClassFolderProcess by input shape not endpoint verb

In luz_docs, `PutUpdatingSecurityClassFolderProcess` and `PatchUpdatingSecurityClassFolderProcess` differ by **input shape**, not by which REST endpoint calls them. Put takes `(currentMetadata, updatedMetadata)` — two full JsonObjects — and gates on `isChangingArrayValue(parentFolderIds, ...)`. Patch takes a JSON-Patch op array, and is only a thin adapter: it finds the `/parentFolderIds` op, re-fetches the folder via `folderService.getFolderById` (extra DB round-trip + security-class visibility check), then delegates to the Put process anyway.

Rule: if the call-site already holds the full before/after metadata (e.g. folder recovery re-parenting), use Put directly — using Patch would mean synthesizing a fake patch document and paying a pointless re-fetch, only to land in the same Put code. Patch exists solely for the real PATCH endpoint where the final state is not known up front.

See [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]].

## Related

- [[Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
