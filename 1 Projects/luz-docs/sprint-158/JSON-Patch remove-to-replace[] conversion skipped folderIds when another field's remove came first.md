---
ai_hash: 41aa2ca9b1a04d34
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities:
- JSON-Patch
- JsonObjectUtil.updatePatchQueryIfRemoveArrayField
- remove (JSON-Patch operation)
- replace [] (JSON-Patch operation)
- folderIds (field)
- securityClassCodes (field)
- MongoDB
- AuditLogDocumentPatchServiceValidation.isCopyDocumentEventPatchRequest
- JsonArray.size()
- NullPointerException (NPE)
- updateArray (method)
- updateOperationIfRemoveArrayField (method)
- return-on-first-match in a loop skips later qualifying elements (pattern)
- document
- currentMetadata.getJsonArray("folderIds") (method call)
- Fix applied (software change)
- Fix 2 (software change)
- JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's
  remove came first (issue)
source: session 2026-06-17
status: seedling
tags:
- luz-docs
- json-patch
- mongodb
- gotcha
- npe
title: JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's
  remove came first
type: lesson
---

# JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first

In \`JsonObjectUtil.updatePatchQueryIfRemoveArrayField\`, the loop that rewrites a JSON-Patch \`remove\` on an array field into \`replace []\` originally did \`return updateArray(...)\` on the **first** op whose operator was REMOVE — but the per-op converter (\`updateOperationIfRemoveArrayField\`) only actually rewrites an op whose **path** matches the target field. So a multi-op patch like \`[{remove /securityClassCodes}, {remove /folderIds}]\` returned at index 0 (a non-matching remove left untouched), and the \`remove /folderIds\` at index 1 was never converted.

**Consequence:** the raw \`remove /folderIds\` reached MongoDB and deleted the field from the document instead of setting it to \`[]\`. On the next PATCH of that now-fieldless doc, \`AuditLogDocumentPatchServiceValidation.isCopyDocumentEventPatchRequest\` did \`currentMetadata.getJsonArray("folderIds").size()\` where \`getJsonArray\` returned \`null\` → NPE ("Cannot invoke JsonArray.size() because currentFolderIds is null").

**Fix applied (root cause only):** change \`return updateArray(...)\` to \`query = updateArray(...)\` so the loop processes ALL matching remove ops. A separate null-guard in the audit validation (Fix 2) is still needed to rescue documents already corrupted before the fix.

This is an instance of the general trap in [[return-on-first-match in a loop skips later qualifying elements]].

## Related

- [[return-on-first-match in a loop skips later qualifying elements]]

%% ai-graph-start %%

**Related notes:**
- [[flattenArrayAddOps runs only in materialize branch]]
- [[json-patch-independent-translation-breaks-reset-then-append]]
- [[RFC-6902 replace at array index expects a scalar element not an array]]
- [[return-on-first-match in a loop skips later qualifying elements]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]

**Relations:**
- JsonObjectUtil.updatePatchQueryIfRemoveArrayField — *converts* — remove (JSON-Patch operation)
- remove (JSON-Patch operation) — *to* — replace [] (JSON-Patch operation)
- JsonObjectUtil.updatePatchQueryIfRemoveArrayField — *uses* — updateArray (method)
- JsonObjectUtil.updatePatchQueryIfRemoveArrayField — *uses* — updateOperationIfRemoveArrayField (method)
- updateOperationIfRemoveArrayField (method) — *rewrites* — JSON-Patch operation
- remove (JSON-Patch operation) — *targets* — folderIds (field)
- remove (JSON-Patch operation) — *targets* — securityClassCodes (field)
- remove (JSON-Patch operation) — *reached* — MongoDB
- remove (JSON-Patch operation) — *deleted* — folderIds (field)
- folderIds (field) — *from* — document
- AuditLogDocumentPatchServiceValidation.isCopyDocumentEventPatchRequest — *invokes* — currentMetadata.getJsonArray("folderIds") (method call)
- currentMetadata.getJsonArray("folderIds") (method call) — *returns* — null
- JsonArray.size() — *invoked on* — null
- JsonArray.size() — *causes* — NullPointerException (NPE)
- Fix applied (software change) — *modifies* — JsonObjectUtil.updatePatchQueryIfRemoveArrayField
- Fix applied (software change) — *changes* — return updateArray(...)
- return updateArray(...) — *to* — query = updateArray(...)
- Fix applied (software change) — *processes all* — remove (JSON-Patch operation)
- Fix 2 (software change) — *is a* — null-guard
- Fix 2 (software change) — *rescues* — documents
- JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first (issue) — *is an instance of* — return-on-first-match in a loop skips later qualifying elements (pattern)
- return-on-first-match in a loop skips later qualifying elements (pattern) — *is related to* — JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first (issue)

%% ai-graph-end %%