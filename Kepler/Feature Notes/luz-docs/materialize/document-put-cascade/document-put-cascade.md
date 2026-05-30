---
ai_hash: 25c3bb366fd9c22a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- document-put-cascade
- Document PUT Cascade
- '@PUT'
- '@Path("/{document-id}")'
- Response
- updateDocumentMetadata
- documentService
- credentialToken
- tenantId
- documentId
- documents
- DocumentResource.java
- C:\Users\dvtliem\Kepler\luz_docs\src\main\java\ch\klara\luz\docs\rest\DocumentResource.java
- PUT /{tenantId}/documents/{document-id}
- document
- materialized technical fields
- API entry point
- Service validation
- Cascade decision gate
- Materialized fields computation
- Save and Side Effects
- Failure Paths
- Files of Record
- Glossary for Newbies
- DocumentService.updateDocumentMetadata
- REST entry point
- full document metadata PUT
- request
- folderIds
- _... fields
- user
- securityClassCode
- _folderNames
- _effectiveSecurityClassCodes
- _isPublic
- folder rename cascade
- diagrams/01-put-flow.png
- Overview diagram
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

1. [[technical-points/01 API Entry Point|API entry point]]
2. [[technical-points/02 Service Validation|Service validation]]
3. [[technical-points/03 Cascade Decision Gate|Cascade decision gate]]
4. [[technical-points/04 Materialized Fields Computation|Materialized fields computation]]
5. [[technical-points/05 Save and Side Effects|Save and side effects]]
6. [[technical-points/06 Failure Paths|Failure paths]]
7. [[technical-points/07 Files of Record|Files of record]]
8. [[technical-points/08 Glossary for Newbies|Glossary for newbies]]

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
- [[01 API Entry Point]]
- [[03 Cascade Decision Gate]]
- [[08 Glossary for Newbies]]
- [[07 Files of Record]]
- [[02 Service Validation]]

**Relations:**
- document-put-cascade — *is a* — Document PUT Cascade
- updateDocumentMetadata — *is annotated with* — @PUT
- updateDocumentMetadata — *is annotated with* — @Path("/{document-id}")
- updateDocumentMetadata — *returns* — Response
- updateDocumentMetadata — *calls* — documentService.updateDocumentMetadata
- documentService.updateDocumentMetadata — *takes parameter* — credentialToken
- documentService.updateDocumentMetadata — *takes parameter* — tenantId
- documentService.updateDocumentMetadata — *takes parameter* — documentId
- documentService.updateDocumentMetadata — *takes parameter* — documents
- updateDocumentMetadata — *is defined in* — DocumentResource.java
- DocumentResource.java — *located at* — C:\Users\dvtliem\Kepler\luz_docs\src\main\java\ch\klara\luz\docs\rest\DocumentResource.java
- document-put-cascade — *explains* — PUT /{tenantId}/documents/{document-id}
- PUT /{tenantId}/documents/{document-id} — *updates* — document
- PUT /{tenantId}/documents/{document-id} — *recomputes* — materialized technical fields
- document-put-cascade — *includes* — API entry point
- document-put-cascade — *includes* — Service validation
- document-put-cascade — *includes* — Cascade decision gate
- document-put-cascade — *includes* — Materialized fields computation
- document-put-cascade — *includes* — Save and Side Effects
- document-put-cascade — *includes* — Failure Paths
- document-put-cascade — *includes* — Files of Record
- document-put-cascade — *includes* — Glossary for Newbies
- updateDocumentMetadata — *is a* — REST entry point
- DocumentService.updateDocumentMetadata — *contains logic for* — full document metadata PUT
- full document metadata PUT — *validates* — request
- full document metadata PUT — *deduplicates* — folderIds
- full document metadata PUT — *checks* — folderIds
- full document metadata PUT — *blocks user edits to* — _... fields
- full document metadata PUT — *decides recomputation of* — materialized technical fields
- materialized technical fields — *recomputed if tenant is* — allowlisted
- materialized technical fields — *recomputed if* — folderIds changed
- materialized technical fields — *recomputed if* — securityClassCode changed
- full document metadata PUT — *recomputes* — _folderNames
- full document metadata PUT — *recomputes* — _effectiveSecurityClassCodes
- full document metadata PUT — *recomputes* — _isPublic
- _folderNames — *means* — Folder names in the same order as folderIds
- _effectiveSecurityClassCodes — *means* — Union of document security codes plus security codes from folders
- _isPublic — *means* — Whether the document should be considered public
- PUT /{tenantId}/documents/{document-id} — *is* — synchronous
- materialized technical fields — *computed before* — document is saved
- PUT /{tenantId}/documents/{document-id} — *is unlike* — folder rename cascade
- Overview diagram — *is* — diagrams/01-put-flow.png

%% ai-graph-end %%