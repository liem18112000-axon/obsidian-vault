---
ai_hash: 71463268e0762809
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
source: session 2026-06-09 MaterializeRepository.java:224 fix
status: seedling
tags:
- luz-docs
- materialize
- java
- gotcha
- http-404
title: luz-docs getDocumentById returns empty object not null for missing docs
type: gotcha
---

# luz-docs getDocumentById returns empty object not null for missing docs

In luz_docs, `JsonStoreMongoService.getDocumentById(token, tenantId, documentId)` returns `JsonObjectUtil.createObjectByString(response.readEntity(String.class))`. For an empty/missing-document body this yields an **empty `JsonObject {}` — never `null`** (it only throws `DocumentException` on a non-200 status).

Consequence: any `Optional.ofNullable(getDocumentById(...))` chain is always *present*, so an `.orElseThrow(() -> new DocumentNotFoundException(...))` 404 branch is **dead**. A missing doc instead falls through to the security check (`MaterializeState.isAccessibleFor({}, codes) == false` → `DocumentMismatchSecurityClassCodeException` 403), or — with `skipSecurityClasses=true` — returns a bogus id-less 200 object. Clients branching on legacy 404 break.

Correct missing-doc test: check the identity field, `doc.get(ID) == null` (`BaseMetadataConstants.ID`). This mirrors the legacy non-materialize path at `JsonStoreMongoService:208`: `jsonObject == null || jsonObject.get(ID) == null`.

Fix applied in `MaterializeRepository.fetchDocumentById` — insert `.filter(doc -> doc.get(ID) != null)` into the Optional chain so the `orElseThrow` 404 becomes reachable again.

This is a concrete instance of [[empty-object-not-null sentinel defeats Optional.ofNullable null-guards]].

## Related

- [[empty-object-not-null sentinel defeats Optional.ofNullable null-guards]]

%% ai-graph-start %%

**Related notes:**
- [[empty-object-not-null sentinel defeats Optional.ofNullable null-guards]]
- [[luz-jsonstore find returns 200 empty string, not [], on zero matches]]
- [[javax.json single-arg getString throws NPE on missing key]]
- [[luz-jsonstore intermittently returns 200 with empty body on folder finds]]
- [[getCollectionMetadataByTerms silently ignored includeDeletedRecord for non-document collections]]

%% ai-graph-end %%