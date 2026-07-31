---
title: "Batched folder delete strips folder ids via union updateMany"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26, commit 0361009"
tags: [luz-docs, mongodb, batch, folder-delete]
---

# Batched folder delete strips folder ids via union updateMany

When a folder is deleted, documents that are linked to the deleted folder *and* other folders must survive — only the deleted folder id should be removed from their `folderIds`. The batched rewrite (luz-docs commit 0361009) does this with a single `updateMany` that applies the **union** of all removed folder ids to every targeted document, instead of one update per document.

This is safe because each document's `folderIds` is a subset of that union: stripping a folder id a document never had is a no-op. So one union strip across the whole batch yields the same result as N per-document strips, but with one round trip instead of N.

The invariant to confirm in review: the mapping that selects which documents to strip (`mapDocumentNeedRemoveFolder`) must only target docs whose filter list is genuinely a subset of the union. Fully-deletable docs (linked only to the deleted folder) go down a separate batched soft-delete path instead.

Related: [[Bulk write paths in folder delete only engage with more than one document]]

## Related

- [[Bulk write paths in folder delete only engage with more than one document]]
