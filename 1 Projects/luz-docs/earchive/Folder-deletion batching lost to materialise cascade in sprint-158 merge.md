---
title: "Folder-deletion batching lost to materialise cascade in sprint-158 merge"
created: 2026-06-12
type: observation
status: seedling
source: "merge e3ce6663b, session 2026-06-12"
tags: [luz-docs, earchive, merge, materialize, folder-deletion]
---

# Folder-deletion batching lost to materialise cascade in sprint-158 merge

Merging origin/master (eArchive materialise cascade) into kepler/sprint-158/enhance-delete-folder-api hit a semantic conflict in `FolderDeletingService.updateDocumentInRemoveFolder`: the branch had batched folder-unlink via one `updateManyRemoveArrayValues` call; master had per-document writes with `materializeFacade.shouldCascadeDocument` → `onDocumentChange` merging recomputed technical fields.

Resolution chosen (2026-06-12, merge e3ce6663b): **master per-doc flow wins; branch batching dropped in that method.** Reason: materialised technical fields are per-document values a shared updateMany pull cannot express. A hybrid (batch for non-cascade docs, per-doc only for cascade docs, cascade docs excluded from batch because updateManyRemoveArrayValues fails verification on matched-but-unmodified) was prototyped and discarded — viable template if the batching perf needs reviving.

## Related

- [[luz_docs parent-change cascade recovers forward]]
- [[not via snapshot rollback]]
