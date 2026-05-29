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
