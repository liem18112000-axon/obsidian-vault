---
ai_hash: 7a0a64beb978bfab
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-12
entities:
- Folder-deletion batching
- Materialise cascade
- Sprint-158 merge
- origin/master
- kepler/sprint-158/enhance-delete-folder-api
- FolderDeletingService.updateDocumentInRemoveFolder
- updateManyRemoveArrayValues
- materializeFacade.shouldCascadeDocument
- onDocumentChange
- Technical fields
- e3ce6663b
- Projects/luz-docs/earchive/luz_docs parent-change cascade
- Snapshot rollback
- Per-document writes
- Semantic conflict
- Resolution
source: merge e3ce6663b, session 2026-06-12
status: seedling
tags:
- luz-docs
- earchive
- merge
- materialize
- folder-deletion
title: Folder-deletion batching lost to materialise cascade in sprint-158 merge
type: observation
---

# Folder-deletion batching lost to materialise cascade in sprint-158 merge

Merging origin/master (eArchive materialise cascade) into kepler/sprint-158/enhance-delete-folder-api hit a semantic conflict in `FolderDeletingService.updateDocumentInRemoveFolder`: the branch had batched folder-unlink via one `updateManyRemoveArrayValues` call; master had per-document writes with `materializeFacade.shouldCascadeDocument` → `onDocumentChange` merging recomputed technical fields.

Resolution chosen (2026-06-12, merge e3ce6663b): **master per-doc flow wins; branch batching dropped in that method.** Reason: materialised technical fields are per-document values a shared updateMany pull cannot express. A hybrid (batch for non-cascade docs, per-doc only for cascade docs, cascade docs excluded from batch because updateManyRemoveArrayValues fails verification on matched-but-unmodified) was prototyped and discarded — viable template if the batching perf needs reviving.

## Related

- [[1 Projects/luz-docs/earchive/luz_docs parent-change cascade recovers forward, not via snapshot rollback]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs parent-change cascade recovers forward, not via snapshot rollback]]
- [[luz-docs delete-folder batching roadmap - remaining per-item paths]]
- [[luz_docs FolderDeletingServiceIT coverage gaps]]
- [[LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master]]
- [[luz_docs folder delete shared document handling]]

**Relations:**
- Folder-deletion batching — *lost to* — Materialise cascade
- Folder-deletion batching — *lost in* — Sprint-158 merge
- Sprint-158 merge — *merges* — origin/master
- Sprint-158 merge — *merges into* — kepler/sprint-158/enhance-delete-folder-api
- origin/master — *implemented* — Materialise cascade
- kepler/sprint-158/enhance-delete-folder-api — *implemented* — Folder-deletion batching
- Folder-deletion batching — *uses* — updateManyRemoveArrayValues
- Materialise cascade — *uses* — Per-document writes
- Per-document writes — *involves* — materializeFacade.shouldCascadeDocument
- materializeFacade.shouldCascadeDocument — *triggers* — onDocumentChange
- onDocumentChange — *merges* — Technical fields
- FolderDeletingService.updateDocumentInRemoveFolder — *experienced* — Semantic conflict
- Semantic conflict — *involved* — Folder-deletion batching
- Semantic conflict — *involved* — Per-document writes
- Resolution — *favored* — Per-document writes
- Resolution — *discarded* — Folder-deletion batching
- Resolution — *recorded as commit* — e3ce6663b
- Technical fields — *are* — per-document values
- Per-document values — *incompatible with* — updateManyRemoveArrayValues
- Projects/luz-docs/earchive/luz_docs parent-change cascade — *recovers* — forward
- Projects/luz-docs/earchive/luz_docs parent-change cascade — *does not use* — Snapshot rollback
- Sprint-158 merge — *related to* — Projects/luz-docs/earchive/luz_docs parent-change cascade

%% ai-graph-end %%