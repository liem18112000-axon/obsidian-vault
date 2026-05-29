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

