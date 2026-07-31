---
ai_hash: aa81367180ea1d9f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Service Validation
- DocumentService
- updateDocumentMetadata
- credentialToken
- tenantId
- documentId
- JsonObject
- metadata
- validation
- validateDocumentId
- validateMatchDocumentId
- jsonStoreMongoService
- getDocumentMetadataById
- currentMetadata
- validateDocumentMetadata
- validateMetadataVersion
- validateDeletionStatus
- validateCorrectFieldsType
- folderIds
- JsonObjectUtil
- removeDuplicate
- folderService
- existFolderIds
- _link
- deleteByKey
- LINK
- validateTechnicalFields
- technical fields
- _folderNames
- Validation
- Current metadata
- New metadata
- Version number
- Deduplicate
- Technical field
- API Entry Point
- Cascade Decision Gate
- document-put-cascade
- invalid IDs
- old document
- document exists
- document is valid
- stale updates
- deletion state
- field types
- repeated folder IDs
- missing folders
- response/navigation data
- internal _... fields
- Materialized fields
- Client
- Service
---

# Service Validation

[[document-put-cascade|Back to index]] | Previous: [[01 API Entry Point|API entry point]] | Next: [[03 Cascade Decision Gate|Cascade decision gate]]

The main method is:

```java
DocumentService.updateDocumentMetadata(String credentialToken, String tenantId, String documentId, JsonObject metadata)
```

Before materialization happens, the service protects the update with several checks.

## Validation sequence

![[diagrams/02-validation.png]]

## Important steps

| Step | Code behavior | Why it matters |
|---|---|---|
| Validate document ID | `validation.validateDocumentId(documentId)` | Rejects invalid IDs early. |
| Match path ID with body ID | `validation.validateMatchDocumentId(documentId, metadata)` | Prevents caller from updating one URL while sending another ID in the body. |
| Load current document | `jsonStoreMongoService.getDocumentMetadataById(...)` | The service needs the old document to compare versions and fields. |
| Validate current metadata | `validation.validateDocumentMetadata(currentMetadata)` | Ensures the document exists and is valid. |
| Validate version | `validateMetadataVersion(...)` | Prevents overwriting someone else's newer update. |
| Validate deletion status | `validateDeletionStatus(...)` | Caller cannot silently change deletion state through this PUT. |
| Validate field types | `validateCorrectFieldsType(metadata, true)` | Ensures fields like arrays really are arrays. |
| Deduplicate `folderIds` | `JsonObjectUtil.removeDuplicate(...)` | Avoids repeated folder IDs. |
| Check folders exist | `folderService.existFolderIds(...)` | Prevents storing references to missing folders. |
| Remove `_link` | `JsonObjectUtil.deleteByKey(..., LINK)` | `_link` is response/navigation data, not data to save. |
| Validate technical fields | `validateTechnicalFields(currentMetadata, metadata)` | Users cannot add/update/delete internal `_...` fields manually. |

## Why technical fields are blocked first

Materialized fields start with `_`, for example `_folderNames`.

The client is not trusted to edit those fields directly. The service first checks that technical fields were not manually changed. Only after that, server code is allowed to recompute and merge the technical fields.

## Technical terms

| Term | Newbie explanation |
|---|---|
| Validation | Checking input before accepting it. |
| Current metadata | The document metadata already stored in the database. |
| New metadata | The JSON body sent by the caller. |
| Version number | A number used to detect stale updates. |
| Deduplicate | Remove repeated values. |
| Technical field | Internal system field, usually starting with `_`, not meant to be edited by users. |
| `_link` | Helper field often used in API responses; it should not be saved back as document metadata. |

%% ai-graph-start %%

**Related notes:**
- [[01 API Entry Point]]
- [[document-put-cascade]]
- [[06 Failure Paths]]
- [[08 Glossary for Newbies]]
- [[05 Save and Side Effects]]

**Relations:**
- updateDocumentMetadata — *is a method of* — DocumentService
- updateDocumentMetadata — *takes parameter* — credentialToken
- updateDocumentMetadata — *takes parameter* — tenantId
- updateDocumentMetadata — *takes parameter* — documentId
- updateDocumentMetadata — *takes parameter* — metadata
- Service Validation — *protects* — updateDocumentMetadata
- validation — *performs* — validateDocumentId
- validateDocumentId — *rejects* — invalid IDs
- validation — *performs* — validateMatchDocumentId
- validateMatchDocumentId — *prevents* — updating one URL while sending another ID in the body
- jsonStoreMongoService — *performs* — getDocumentMetadataById
- getDocumentMetadataById — *loads* — old document
- validation — *performs* — validateDocumentMetadata
- validateDocumentMetadata — *ensures* — document exists
- validateDocumentMetadata — *ensures* — document is valid
- validateMetadataVersion — *prevents* — stale updates
- validateDeletionStatus — *prevents* — caller from silently changing deletion state
- validateCorrectFieldsType — *ensures* — field types
- JsonObjectUtil — *performs* — removeDuplicate
- removeDuplicate — *operates on* — folderIds
- removeDuplicate — *avoids* — repeated folder IDs
- folderService — *performs* — existFolderIds
- existFolderIds — *prevents* — storing references to missing folders
- JsonObjectUtil — *performs* — deleteByKey
- deleteByKey — *removes* — _link
- _link — *is* — response/navigation data
- validateTechnicalFields — *validates* — technical fields
- validateTechnicalFields — *blocks* — users from adding/updating/deleting internal _... fields
- Materialized fields — *start with* — `_`
- _folderNames — *is an example of* — Materialized fields
- Client — *is not trusted to edit* — technical fields
- Service — *recomputes and merges* — technical fields
- Validation — *is defined as* — Checking input before accepting it
- Current metadata — *is defined as* — The document metadata already stored in the database
- New metadata — *is defined as* — The JSON body sent by the caller
- Version number — *is defined as* — A number used to detect stale updates
- Deduplicate — *is defined as* — Remove repeated values
- Technical field — *is defined as* — Internal system field
- Technical field — *starts with* — `_`
- _link — *is defined as* — Helper field often used in API responses
- Service Validation — *back to* — document-put-cascade
- Service Validation — *previous step is* — API Entry Point
- Service Validation — *next step is* — Cascade Decision Gate

%% ai-graph-end %%