---
ai_hash: 4ece1f404b02f59b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Glossary for Newbies
- document PUT cascade notes
- document-put-cascade
- index
- Visual glossary
- API and Java terms
- Document and metadata terms
- Materialization terms
- API
- HTTP PUT
- Path parameter
- Header parameter
- Method
- Service
- Validation
- Exception
- Document metadata
- '`folderIds`'
- '`securityClassCode`'
- Technical field
- '`_folderNames`'
- '`_effectiveSecurityClassCodes`'
- '`_isPublic`'
- Materialized field
- Cascade
- Trigger field
- Allowlist
- Tenant
- Synchronous
- Retry
- Fallback
---

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

%% ai-graph-start %%

**Related notes:**
- [[03 Cascade Decision Gate]]
- [[document-put-cascade]]
- [[04 Materialized Fields Computation]]
- [[09 Glossary for Newbies]]
- [[02 Service Validation]]

**Relations:**
- Glossary for Newbies — *explains terms used in* — document PUT cascade notes
- Glossary for Newbies — *links to* — document-put-cascade
- document-put-cascade — *links to* — index
- Glossary for Newbies — *includes section* — Visual glossary
- Glossary for Newbies — *includes section* — API and Java terms
- Glossary for Newbies — *includes section* — Document and metadata terms
- Glossary for Newbies — *includes section* — Materialization terms
- API — *is a term in* — API and Java terms
- API — *is defined as* — A way for software to talk to other software.
- HTTP PUT — *is a term in* — API and Java terms
- HTTP PUT — *is defined as* — A request that usually replaces or updates a whole resource.
- Path parameter — *is a term in* — API and Java terms
- Path parameter — *is defined as* — A value inside the URL path, like `{document-id}`.
- Header parameter — *is a term in* — API and Java terms
- Header parameter — *is defined as* — A value sent in request headers, like `credentialToken`.
- Method — *is a term in* — API and Java terms
- Method — *is defined as* — A block of Java code that can be called.
- Service — *is a term in* — API and Java terms
- Service — *is defined as* — Code that contains business logic.
- Validation — *is a term in* — API and Java terms
- Validation — *is defined as* — Checking whether input is allowed before using it.
- Exception — *is a term in* — API and Java terms
- Exception — *is defined as* — Java's way to signal an error.
- Document metadata — *is a term in* — Document and metadata terms
- Document metadata — *is defined as* — Information about a document, such as folder IDs, security codes, version, and file data.
- `folderIds` — *is a term in* — Document and metadata terms
- `folderIds` — *is defined as* — List of folders that contain or categorize the document.
- `securityClassCode` — *is a term in* — Document and metadata terms
- `securityClassCode` — *is defined as* — List of access-control labels directly assigned to the document.
- Technical field — *is a term in* — Document and metadata terms
- Technical field — *is defined as* — Internal field used by the system, usually starting with `_`.
- `_folderNames` — *is a term in* — Document and metadata terms
- `_folderNames` — *is defined as* — Cached folder names matching the order of `folderIds`.
- `_effectiveSecurityClassCodes` — *is a term in* — Document and metadata terms
- `_effectiveSecurityClassCodes` — *is defined as* — Combined security codes from the document and its folders.
- `_isPublic` — *is a term in* — Document and metadata terms
- `_isPublic` — *is defined as* — Cached boolean saying whether the document is public according to the materialized calculation.
- Materialized field — *is a term in* — Materialization terms
- Materialized field — *is defined as* — A computed field saved in the database to make future reads faster or simpler.
- Cascade — *is a term in* — Materialization terms
- Cascade — *is defined as* — Updating related computed fields because source fields changed.
- Trigger field — *is a term in* — Materialization terms
- Trigger field — *is defined as* — A field that causes materialization to rerun when changed.
- Allowlist — *is a term in* — Materialization terms
- Allowlist — *is defined as* — Configuration that enables a feature for selected tenants.
- Tenant — *is a term in* — Materialization terms
- Tenant — *is defined as* — One customer or organization using the system.
- Synchronous — *is a term in* — Materialization terms
- Synchronous — *is defined as* — The request waits until the work finishes.
- Retry — *is a term in* — Materialization terms
- Retry — *is defined as* — Trying again after a temporary failure.
- Fallback — *is a term in* — Materialization terms
- Fallback — *is defined as* — Backup behavior after retries fail.

%% ai-graph-end %%