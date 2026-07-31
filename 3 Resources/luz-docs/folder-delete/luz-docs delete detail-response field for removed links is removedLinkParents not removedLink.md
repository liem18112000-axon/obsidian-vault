---
title: "luz-docs delete detail-response field for removed links is removedLinkParents not removedLink"
created: 2026-06-26
type: lesson
status: seedling
source: "session 2026-06-26, IT run"
tags: [luz-docs, api, testing, gotcha, folder-delete]
---

# luz-docs delete detail-response field for removed links is removedLinkParents not removedLink

The DELETE /folders/{id}?is-detail-response=true response reports documents that lost a link to the deleted folder under the JSON key **`removedLinkParents`** — NOT `removedLink`. Source: `Constants.REMOVED_LINK = "removedLinkParents"`. Shape: a list with one entry per surviving document, each entry `{documentId: [removedFolderId, ...]}`. So a non-zero removed-link count is `len(removedLinkParents)`, and `deletedDocuments` holds the fully-deleted doc ids.

**Latent-bug trap:** the existing integration-test steps in `delete_folder_steps.py` read `detail.get('removedLink', [])`, which is the wrong key — but every existing scenario only ever asserts `"0" removedLink`, so the missing key returns `[]`, length 0, and the assertion passes anyway. The wrong field name stayed hidden until a new test asserted a *non-zero* removed link and got 0 instead of the expected count.

Lesson: a test that only ever checks the zero/empty case can mask a wrong field name (typo'd keys return the default). The first non-zero assertion is what actually validates the field path.

Related: [[Batched folder delete strips folder ids via union updateMany]], [[Bulk write paths in folder delete only engage with more than one document]]

## Related

- [[Batched folder delete strips folder ids via union updateMany]]
- [[Bulk write paths in folder delete only engage with more than one document]]
