---
title: "Luz delete-folder tests can only delete public folders, not ones carrying a security class"
created: 2026-06-08
type: gotcha
status: seedling
source: "session 2026-06-08 LUZ-154157"
tags: [luz-docs, security-class, testing, delete-folder, gotcha]
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

- [[Luz _folderSecurityClassCodes is a list-of-lists]]
- [[one inner list per folder]]
