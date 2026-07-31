---
title: "DocumentService.recoverDocument re-stamps materialized fields via the cascade diff"
created: 2026-06-15
type: concept
status: seedling
source: "session 2026-06-15"
tags: [luz-docs, materialize, recover]
---

# DocumentService.recoverDocument re-stamps materialized fields via the cascade diff

On recovering a soft-deleted document, `DocumentService.recoverDocument` re-stamps the materialized folder fields (`_isPublic`, `_effectiveSecurityClassCodes`, `_folderNames`) when the materialize cascade should run:

```java
if (materializeFacade.shouldCascadeDocument(tenantId, metadataBeforeRecovery, currentMetadata)) {
    currentMetadata = JsonObjectUtil.mergeTechnicalFields(
        currentMetadata, materializeFacade.onDocumentChange(token, tenantId, currentMetadata));
}
```

`metadataBeforeRecovery` is the deleted doc as-is; `currentMetadata` is after the folderIds (from the restore body) and deletionStatus changes are applied. `MaterializeCascadeService.shouldCascadeDocument` fires when folderIds differ (compared ORDER-SENSITIVELY, because `_folderNames`/codes are positionally aligned) OR securityClassCode differs (compared as an ORDER-INSENSITIVE set).

Testing guidance: assert the OBSERVABLE outcome — after recovery the materialized fields reflect the document's final folder membership + its own doc-level codes — rather than whether the cascade internally fired (you cannot observe that, and soft-delete may itself have changed folderIds).

Related: [[JSON-driven Scenario Outline pattern for luz_docs materialize integration tests]], [[document_service.recover_document drops an empty folderIds list, so it cannot clear folders]]

## Related

- [[JSON-driven Scenario Outline pattern for luz_docs materialize integration tests]]
- [[3 Resources/Work-Kepler/luz-docs/integration-test/document_service.recover_document drops an empty folderIds list, so it cannot clear folders]]
