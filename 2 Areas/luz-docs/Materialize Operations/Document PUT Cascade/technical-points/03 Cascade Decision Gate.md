---
ai_hash: a932590a35160c9d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Cascade Decision Gate
- document-put-cascade
- 02 Service Validation
- 04 Materialized Fields Computation
- materializeFacade
- metadata
- currentMetadata
- recomputing materialized fields
- diagrams/03-decision-gate.png
- cascade
- materialization allowlist
- materialization trigger field
- DocumentMetadataConstants.FOLDER_IDS
- BaseMetadataConstants.SECURITY_CLASSCODE
- folderIds
- _folderNames
- security inherited from folders
- securityClassCode
- _effectiveSecurityClassCodes
- _isPublic
- MaterializeCascadeService.shouldCascadeDocument
- sets
- order-only changes
- materialization
- Decision gate
- Allowlist
- Trigger field
- Set
- Cascade
- JsonObjectUtil
---

# Cascade Decision Gate

[[document-put-cascade|Back to index]] | Previous: [[02 Service Validation|Service validation]] | Next: [[04 Materialized Fields Computation|Materialized fields computation]]

After validation, the service asks:

```java
if (materializeFacade.shouldCascadeDocument(tenantId, currentMetadata, metadata)) {
    metadata = JsonObjectUtil.mergeTechnicalFields(
        metadata,
        materializeFacade.onDocumentChange(token, tenantId, metadata)
    );
}
```

This is the decision gate for recomputing materialized fields.

## Visual explanation

![[diagrams/03-decision-gate.png]]

## When does the cascade run?

It runs only when both conditions are true:

1. The tenant is enabled in the materialization allowlist.
2. The update changed at least one materialization trigger field.

The trigger fields are:

```java
Set.of(DocumentMetadataConstants.FOLDER_IDS, BaseMetadataConstants.SECURITY_CLASSCODE)
```

In plain English:

| Trigger field | Why it matters |
|---|---|
| `folderIds` | Changing folders changes `_folderNames`, and can change security inherited from folders. |
| `securityClassCode` | Changing the document's own security codes changes `_effectiveSecurityClassCodes` and `_isPublic`. |

## Important detail: comparison uses sets

`MaterializeCascadeService.shouldCascadeDocument` compares the old and new trigger fields as sets:

```java
!asSet(currentDocument, field).equals(asSet(newDocument, field))
```

That means order-only changes do **not** trigger materialization.

Example:

```text
Old folderIds: [f1, f2]
New folderIds: [f2, f1]
```

As sets, both are `{f1, f2}`, so the cascade does not run.

## Technical terms

| Term | Newbie explanation |
|---|---|
| Decision gate | An `if` check that decides whether special logic runs. |
| Allowlist | A list of tenants for which a feature is enabled. |
| Trigger field | A field where changes should cause other computed fields to refresh. |
| Set | A collection where order does not matter and duplicates are ignored. |
| Cascade | One change causes other related values to update. |
| Materialization | Saving computed values so later reads do not need to compute them again. |

%% ai-graph-start %%

**Related notes:**
- [[04 Materialized Fields Computation]]
- [[08 Glossary for Newbies]]
- [[document-put-cascade]]
- [[03 Cascade Attempt]]
- [[01 Overview - Folder Rename Cascade]]

**Relations:**
- Cascade Decision Gate — *is part of* — document-put-cascade
- Cascade Decision Gate — *follows* — 02 Service Validation
- Cascade Decision Gate — *precedes* — 04 Materialized Fields Computation
- Cascade Decision Gate — *is a* — Decision gate
- Cascade Decision Gate — *controls* — recomputing materialized fields
- Cascade Decision Gate — *uses* — materializeFacade
- materializeFacade — *calls* — MaterializeCascadeService.shouldCascadeDocument
- materializeFacade — *calls* — onDocumentChange
- JsonObjectUtil — *merges fields* — metadata
- cascade — *runs if tenant in* — materialization allowlist
- cascade — *runs if* — materialization trigger field
- materialization trigger field — *includes* — DocumentMetadataConstants.FOLDER_IDS
- materialization trigger field — *includes* — BaseMetadataConstants.SECURITY_CLASSCODE
- folderIds — *changes* — _folderNames
- folderIds — *changes* — security inherited from folders
- securityClassCode — *changes* — _effectiveSecurityClassCodes
- securityClassCode — *changes* — _isPublic
- MaterializeCascadeService.shouldCascadeDocument — *compares fields as* — sets
- sets — *ignore* — order-only changes
- order-only changes — *do not trigger* — materialization
- diagrams/03-decision-gate.png — *illustrates* — Cascade Decision Gate
- Decision gate — *is defined as* — An if check that decides whether special logic runs
- Allowlist — *is defined as* — A list of tenants for which a feature is enabled
- Trigger field — *is defined as* — A field where changes should cause other computed fields to refresh
- Set — *is defined as* — A collection where order does not matter and duplicates are ignored
- Cascade — *is defined as* — One change causes other related values to update
- materialization — *is defined as* — Saving computed values so later reads do not need to compute them again
- metadata — *is updated by* — onDocumentChange
- currentMetadata — *is compared with* — metadata

%% ai-graph-end %%