---
ai_hash: 1d4d60de07bb49bf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26, commit 0361009
status: seedling
tags:
- luz-docs
- mongodb
- batch
- folder-delete
title: Batched folder delete strips folder ids via union updateMany
type: lesson
---

# Batched folder delete strips folder ids via union updateMany

When a folder is deleted, documents that are linked to the deleted folder *and* other folders must survive — only the deleted folder id should be removed from their `folderIds`. The batched rewrite (luz-docs commit 0361009) does this with a single `updateMany` that applies the **union** of all removed folder ids to every targeted document, instead of one update per document.

This is safe because each document's `folderIds` is a subset of that union: stripping a folder id a document never had is a no-op. So one union strip across the whole batch yields the same result as N per-document strips, but with one round trip instead of N.

The invariant to confirm in review: the mapping that selects which documents to strip (`mapDocumentNeedRemoveFolder`) must only target docs whose filter list is genuinely a subset of the union. Fully-deletable docs (linked only to the deleted folder) go down a separate batched soft-delete path instead.

Related: [[Bulk write paths in folder delete only engage with more than one document]]

## Related

- [[Bulk write paths in folder delete only engage with more than one document]]

%% ai-graph-start %%

**Related notes:**
- [[Bulk write paths in folder delete only engage with more than one document]]
- [[luz_docs folder delete shared document handling]]
- [[Removing the union of array values per document is safe because absent values are no-ops]]
- [[luz-docs delete-folder batching roadmap - remaining per-item paths]]
- [[luz-docs folder delete filter double-fetched every subfolder]]

%% ai-graph-end %%