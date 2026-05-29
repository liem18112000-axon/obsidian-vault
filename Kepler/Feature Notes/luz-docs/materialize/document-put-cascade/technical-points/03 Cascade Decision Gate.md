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

