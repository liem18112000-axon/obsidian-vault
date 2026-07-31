---
title: "JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first"
created: 2026-06-17
type: lesson
status: seedling
source: "session 2026-06-17"
tags: [luz-docs, json-patch, mongodb, gotcha, npe]
---

# JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first

In \`JsonObjectUtil.updatePatchQueryIfRemoveArrayField\`, the loop that rewrites a JSON-Patch \`remove\` on an array field into \`replace []\` originally did \`return updateArray(...)\` on the **first** op whose operator was REMOVE — but the per-op converter (\`updateOperationIfRemoveArrayField\`) only actually rewrites an op whose **path** matches the target field. So a multi-op patch like \`[{remove /securityClassCodes}, {remove /folderIds}]\` returned at index 0 (a non-matching remove left untouched), and the \`remove /folderIds\` at index 1 was never converted.

**Consequence:** the raw \`remove /folderIds\` reached MongoDB and deleted the field from the document instead of setting it to \`[]\`. On the next PATCH of that now-fieldless doc, \`AuditLogDocumentPatchServiceValidation.isCopyDocumentEventPatchRequest\` did \`currentMetadata.getJsonArray("folderIds").size()\` where \`getJsonArray\` returned \`null\` → NPE ("Cannot invoke JsonArray.size() because currentFolderIds is null").

**Fix applied (root cause only):** change \`return updateArray(...)\` to \`query = updateArray(...)\` so the loop processes ALL matching remove ops. A separate null-guard in the audit validation (Fix 2) is still needed to rescue documents already corrupted before the fix.

This is an instance of the general trap in [[return-on-first-match in a loop skips later qualifying elements]].

## Related

- [[return-on-first-match in a loop skips later qualifying elements]]
