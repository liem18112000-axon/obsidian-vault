---
title: "luz_docs change tracking phase 1 is scoped to the documents collection only"
created: 2026-06-05
type: observation
status: budding
source: "TrackedFields scoping, session 2026-06-05"
tags: [luz-docs, change-tracking, design-decision]
---

# luz_docs change tracking phase 1 is scoped to the documents collection only

luz_docs jsonstore change tracking (`TrackedFields`) is deliberately scoped to the **documents collection only** in phase 1 (decided 2026-06-05 with Liem): `FIELDS_BY_COLLECTION` maps just `documents -> {folderIds, securityClassCodes}`. Folder writes (rename, parent-change, security-code edits) do NOT fire change events — those cascades remain service-layer driven (explicit calls in the folder update paths, e.g. the rename cascade with markers).

Why: the only event consumer is `DocMaterializeObserver`, which recomputes per-document sentinels — it already gates on the documents collection, so folder events had no consumer; tracking them only added pre-read cost to every folder write. When a folder-event consumer appears (e.g. an observer-driven subtree cascade), re-add `folders -> {securityClassCodes, inheritedSecurityClassCodes, parentFolderIds}` to the map — the rest of the pipeline (gates, diff, events) is collection-generic and needs no change.

## Related

- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[luz_docs has two materialize cascade delivery mechanisms]]
