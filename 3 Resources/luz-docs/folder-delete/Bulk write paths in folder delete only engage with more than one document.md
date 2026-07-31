---
title: "Bulk write paths in folder delete only engage with more than one document"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26"
tags: [luz-docs, testing, integration-test, batch]
---

# Bulk write paths in folder delete only engage with more than one document

A bulk/batched write path in a delete cascade only actually runs the batch logic when there is more than one item to batch. In luz-docs DELETE /folders/{id}, the `updateMany` soft-delete and the union folder-id strip only engage when a folder holds **>1 document** — with a single document the code is indistinguishable from the old per-document path.

**Consequence for integration tests:** every pre-existing folder-delete IT scenario used exactly one document, so none of them exercised the batched code at all. A meaningful IT for the batch change must delete one folder holding *several* documents at once, mixing:
- fully-deletable docs (linked only to the deleted folder) → verifies the batch soft-delete, and
- a doc also linked to a second folder → verifies the union folder-id strip leaves only the surviving folder.

Detail-response counts (deletedDocuments, removedLink) are the cheapest assertion that the batch processed the right set.

General lesson: when testing a 'batched' or 'bulk' rewrite, the minimal-but-meaningful fixture is N>1, not N=1 — N=1 silently bypasses the very thing under test.

Related: [[Batched folder delete strips folder ids via union updateMany]], [[Minimal meaningful test fixture size is bounded by the real page size]]

## Related

- [[Batched folder delete strips folder ids via union updateMany]]
- [[Minimal meaningful test fixture size is bounded by the real page size]]
