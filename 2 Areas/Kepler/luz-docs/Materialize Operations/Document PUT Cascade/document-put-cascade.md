---
ai_hash: 21c0a32eacdcf816
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities:
- document-put-cascade
- Document PUT Cascade
- Source endpoint
- '@PUT'
- '@Path'
- Response
- DocumentResource.updateDocumentMetadata
- documentService
- credentialToken
- tenantId
- documentId
- documents
- DocumentResource.java
- C:\Users\dvtliem\Kepler\luz_docs\src\main\java\ch\klara\luz_docs\rest\DocumentResource.java
- PUT /{tenantId}/documents/{document-id}
- materialized technical fields
- API Entry Point
- Service Validation
- Cascade Decision Gate
- Materialized Fields Computation
- Save and Side Effects
- Failure Paths
- Files of Record
- Glossary for Newbies
- REST entry point
- DocumentService.updateDocumentMetadata
- full document metadata PUT
- service
- request
- folderIds
- technical fields
- user edits
- tenant
- securityClassCode
- _folderNames
- _effectiveSecurityClassCodes
- _isPublic
- folder rename cascade
- synchronous
- derived fields
- document
- diagrams/01-put-flow.png
---

# Document PUT Cascade

Source endpoint:

```java
@PUT
@Path("/{document-id}")
public Response updateDocumentMetadata(...) {
    documentService.updateDocumentMetadata(credentialToken, tenantId, documentId, documents);
    return Response.ok().build();
}
```

Code location:

`C:\Users\dvtliem\Kepler\luz_docs\src\main\java\ch\klara\luz\docs\rest\DocumentResource.java`

## Start here

This note set explains how `PUT /{tenantId}/documents/{document-id}` updates a document and, when needed, recomputes the materialized technical fields.

Read in this order:

1. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/01 API Entry Point|API entry point]]
2. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/02 Service Validation|Service validation]]
3. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/03 Cascade Decision Gate|Cascade decision gate]]
4. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/04 Materialized Fields Computation|Materialized fields computation]]
5. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/05 Save and Side Effects|Save and side effects]]
6. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/06 Failure Paths|Failure paths]]
7. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/07 Files of Record|Files of record]]
8. [[2 Areas/Kepler/luz-docs/Materialize Operations/Document PUT Cascade/technical-points/08 Glossary for Newbies|Glossary for newbies]]

## TL;DR

`DocumentResource.updateDocumentMetadata` is only the REST entry point. The real logic is in `DocumentService.updateDocumentMetadata`.

On a full document metadata `PUT`, the service validates the request, deduplicates and checks `folderIds`, blocks user edits to technical `_...` fields, then decides whether materialized fields must be recomputed.

If the tenant is allowlisted and either `folderIds` or `securityClassCode` changed, the code recomputes:

| Field | Meaning |
|---|---|
| `_folderNames` | Folder names in the same order as `folderIds`. |
| `_effectiveSecurityClassCodes` | Union of document security codes plus security codes from folders. |
| `_isPublic` | Whether the document should be considered public. |

Unlike folder rename cascade, this `PUT` path is **synchronous**: the derived fields are computed before the document is saved.

## Overview diagram

![[diagrams/01-put-flow.png]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs folder security-class changes have 3 entry points but only PUT cascades]]
- [[01 API Entry Point]]
- [[DocumentService.recoverDocument re-stamps materialized fields via the cascade diff]]
- [[07 Files of Record]]
- [[03 Cascade Decision Gate]]

**Relations:**
- document-put-cascade — *IS_ABOUT* — Document PUT Cascade
- Source endpoint — *USES* — @PUT
- Source endpoint — *USES* — @Path
- Source endpoint — *RETURNS* — Response
- Source endpoint — *INVOKES* — DocumentResource.updateDocumentMetadata
- DocumentResource.updateDocumentMetadata — *IS_DEFINED_IN* — DocumentResource.java
- DocumentResource.java — *IS_LOCATED_AT* — C:\Users\dvtliem\Kepler\luz_docs\src\main\java\ch\klara\luz_docs\rest\DocumentResource.java
- DocumentResource.updateDocumentMetadata — *CALLS* — documentService.updateDocumentMetadata
- documentService.updateDocumentMetadata — *USES* — credentialToken
- documentService.updateDocumentMetadata — *USES* — tenantId
- documentService.updateDocumentMetadata — *USES* — documentId
- documentService.updateDocumentMetadata — *USES* — documents
- PUT /{tenantId}/documents/{document-id} — *IS_EXPLAINED_BY* — document-put-cascade
- PUT /{tenantId}/documents/{document-id} — *UPDATES* — document
- PUT /{tenantId}/documents/{document-id} — *RECOMPUTES* — materialized technical fields
- document-put-cascade — *HAS_STEP* — API Entry Point
- document-put-cascade — *HAS_STEP* — Service Validation
- document-put-cascade — *HAS_STEP* — Cascade Decision Gate
- document-put-cascade — *HAS_STEP* — Materialized Fields Computation
- document-put-cascade — *HAS_STEP* — Save and Side Effects
- document-put-cascade — *HAS_STEP* — Failure Paths
- document-put-cascade — *HAS_STEP* — Files of Record
- document-put-cascade — *HAS_STEP* — Glossary for Newbies
- DocumentResource.updateDocumentMetadata — *IS_A* — REST entry point
- DocumentService.updateDocumentMetadata — *CONTAINS* — real logic
- full document metadata PUT — *TRIGGERS* — service
- service — *VALIDATES* — request
- service — *DEDUPLICATES* — folderIds
- service — *CHECKS* — folderIds
- service — *BLOCKS* — user edits
- user edits — *TO* — technical fields
- service — *DECIDES* — materialized fields recomputation
- materialized fields — *ARE_RECOMPUTED_IF* — tenant is allowlisted
- materialized fields — *ARE_RECOMPUTED_IF* — folderIds changed
- materialized fields — *ARE_RECOMPUTED_IF* — securityClassCode changed
- recomputed fields — *INCLUDE* — _folderNames
- recomputed fields — *INCLUDE* — _effectiveSecurityClassCodes
- recomputed fields — *INCLUDE* — _isPublic
- _folderNames — *DESCRIBES* — Folder names in the same order as folderIds
- _effectiveSecurityClassCodes — *DESCRIBES* — Union of document security codes plus security codes from folders
- _isPublic — *DESCRIBES* — Whether the document should be considered public
- PUT path — *IS* — synchronous
- derived fields — *ARE_COMPUTED_BEFORE* — document is saved
- document-put-cascade — *HAS_OVERVIEW_DIAGRAM* — diagrams/01-put-flow.png
- PUT path — *IS_DIFFERENT_FROM* — folder rename cascade

%% ai-graph-end %%