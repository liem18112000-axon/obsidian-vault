---
ai_hash: c9a629ac2ac7cdce
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-08
entities: []
source: session 2026-06-08 LUZ-154157
status: seedling
tags:
- luz-docs
- security-class
- testing
- delete-folder
- gotcha
title: Luz delete-folder tests can only delete public folders, not ones carrying a
  security class
type: gotcha
---

# Luz delete-folder tests can only delete public folders, not ones carrying a security class

In luz_docs_integration_test (dev cluster / local luz-docs), the all-tenant test token **cannot delete a folder that carries a securityClassCode it does not effectively hold** (e.g. `SC1_4`). `DELETE /{tenant}/folders/{id}` runs `getFolderById` + security verification first; when the token can't access the secured folder it returns **404 `folder.id.is.not.found`** (not 403).

Asymmetry that hides this:
- **Creating** a folder/doc with `SC1_4`/`SC2_2` succeeds, and materialize-on-create stamps the codes fine.
- **Reading** via `get-documents-by-ids` bypasses the security ACL, so reads of secured docs work.
- So the existing `materialize/update` tests (which only delete secured folders in exception-swallowing *cleanup*) never surface this — but a test whose **action** is deleting a secured folder fails with 404.

Practical rule for delete-folder tests with this token: the **deleted/target folder must be public**; security codes may only live on **surviving** folders (reading/restamping survivors is fine). Consequence: a delete-driven `_isPublic` flip or code-drop is **not testable** here — you can only assert that surviving folders' codes/names are retained or unioned.

Verified 2026-06-08: case_01/02 (public target) pass; case_03/04 (target carries SC1_4) 404 on delete.

## Related
[[Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder]]

## Related

- [[3 Resources/Work-Kepler/luz-docs/materialize/Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder]]

%% ai-graph-start %%

**Related notes:**
- [[Luz _folderSecurityClassCodes is a list-of-lists, one inner list per folder]]
- [[luz_docs FolderDeletingServiceIT coverage gaps]]
- [[luz_docs deleteFolder isDetailResponse error contract and non-transactionality]]
- [[luz_docs delete folder API soft vs permanent state machine]]
- [[luz-docs folder delete verifies document security classes with one limit-1 Mongo query per folder]]

%% ai-graph-end %%