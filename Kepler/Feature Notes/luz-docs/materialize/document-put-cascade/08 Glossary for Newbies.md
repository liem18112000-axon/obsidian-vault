# Glossary for Newbies

[[document-put-cascade|Back to index]]

This glossary explains the technical terms used in the document PUT cascade notes.

## Visual glossary

![[diagrams/08-glossary-map.png]]

## API and Java terms

| Term | Newbie explanation |
|---|---|
| API | A way for software to talk to other software. |
| HTTP PUT | A request that usually replaces or updates a whole resource. |
| Path parameter | A value inside the URL path, like `{document-id}`. |
| Header parameter | A value sent in request headers, like `credentialToken`. |
| Method | A block of Java code that can be called. |
| Service | Code that contains business logic. |
| Validation | Checking whether input is allowed before using it. |
| Exception | Java's way to signal an error. |

## Document and metadata terms

| Term | Newbie explanation |
|---|---|
| Document metadata | Information about a document, such as folder IDs, security codes, version, and file data. |
| `folderIds` | List of folders that contain or categorize the document. |
| `securityClassCode` | List of access-control labels directly assigned to the document. |
| Technical field | Internal field used by the system, usually starting with `_`. |
| `_folderNames` | Cached folder names matching the order of `folderIds`. |
| `_effectiveSecurityClassCodes` | Combined security codes from the document and its folders. |
| `_isPublic` | Cached boolean saying whether the document is public according to the materialized calculation. |

## Materialization terms

| Term | Newbie explanation |
|---|---|
| Materialized field | A computed field saved in the database to make future reads faster or simpler. |
| Cascade | Updating related computed fields because source fields changed. |
| Trigger field | A field that causes materialization to rerun when changed. |
| Allowlist | Configuration that enables a feature for selected tenants. |
| Tenant | One customer or organization using the system. |
| Synchronous | The request waits until the work finishes. |
| Retry | Trying again after a temporary failure. |
| Fallback | Backup behavior after retries fail. |

