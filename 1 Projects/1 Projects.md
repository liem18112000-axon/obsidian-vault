---
title: "Projects"
type: moc
---

# Projects

> Short-term efforts with a goal and a deadline. Things you are actively finishing right now.

## luz-docs

### Folder Recovery and Security Cascade

Notes around LUZ-155107 / folder recovery, inherited security recomputation, and recovery test strategy.

- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/FolderService.recoverFolder is not materialize-aware]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/Folder recovery must recompute inherited security after deletion statuses are cleared]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/Folder recovery re-parenting must recompute inheritedSecurityClassCode like the PUT path]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]]
- [[1 Projects/luz-docs/Folder Recovery and Security Cascade/LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master]]

### Materialize Cascades

Active implementation notes for document materialization cascades.

- [[1 Projects/luz-docs/materialize/cascades/Folder recovery reuses the parent-change materialize cascade]]
- [[1 Projects/luz-docs/materialize/cascades/Materialize folder parentFolderIds change cascade (LUZ-154159)]]
- [[1 Projects/luz-docs/materialize/cascades/Folder Rename Cascade/Folder parent-change cascade is a no-op when the security-code union is unchanged]]
- [[1 Projects/luz-docs/materialize/cascades/Folder Rename Cascade/_folderNames is parent-chain-independent — depends only on each folder's own name]]

### JsonStore Change Tracking

- [[1 Projects/luz-docs/JsonStore Change Tracking/luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]

### eArchive Integration

- [[1 Projects/luz-docs/earchive/integration/Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
- [[1 Projects/luz-docs/earchive/integration/eArchive PRs in luz_docs target earchive-master integration branch, not master]]
- [[1 Projects/luz-docs/earchive/integration/Put vs Patch UpdatingSecurityClassFolderProcess prefix denotes input shape, not logic]]
