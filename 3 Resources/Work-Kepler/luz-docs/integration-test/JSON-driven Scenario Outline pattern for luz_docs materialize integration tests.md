---
ai_hash: fe11c051afba8202
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-15
entities: []
source: session 2026-06-15
status: seedling
tags:
- luz-docs
- integration-test
- materialize
- behave
title: JSON-driven Scenario Outline pattern for luz_docs materialize integration tests
type: howto
---

# JSON-driven Scenario Outline pattern for luz_docs materialize integration tests

luz_docs materialize integration tests (create/update/recover) share one shape: a behave Scenario Outline whose Examples rows each name a JSON case file. Each case JSON declares:
- `folders`: ordered list with `securityClassCodes`, optional `inheritedSecurityClassCodes`, optional `parent` (name reference to an earlier folder)
- `initial`: inline document `metadata` + `folderIndices` into the created folders
- the mutation (`update` / `recover`: `folderIndices`, [] = root)
- `expected`: `_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`

Two load-bearing technique choices:
1. Create the initial doc via the LOW-LEVEL `luz_docs_rest_client.create_document` multipart (not `document_service.create_document`, which only reads metadata from a FILE PATH) so doc-level `securityClassCodes` can be embedded inline in the case JSON.
2. Re-fetch the result via `get_documents_by_ids` (get-by-ids), NOT a plain GET: a plain GET enforces the security-class ACL and 403s once the doc carries codes the token does not fully hold; get-by-ids returns the doc so the materialized fields can be asserted.

Resolve `$SCn` placeholders across the whole loaded case at once with `security_class_util.resolve(context.access_token, raw)`.

Related: [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]], [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

## Related

- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs IT $SCn security-class placeholders resolve against the token claim]]
- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]
- [[Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]]
- [[luz-docs IT $SCn authorization failures were deterministic resolution regressions, not membership]]
- [[Luz delete-folder tests can only delete public folders, not ones carrying a security class]]

%% ai-graph-end %%