---
ai_hash: 3c7558032f917a17
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
tags:
- json-patch
- rfc6902
- mongodb
- luz-docs
- gotcha
title: JSON-Patch independent translation breaks reset-then-append on the same array
---

# JSON-Patch independent translation breaks reset-then-append on the same array

**Gotcha:** If you translate each JSON-Patch (RFC 6902) op *independently against the original document* instead of applying ops *sequentially*, a patch that resets an array and then appends to it in the same request silently loses the appended value.

Concrete case (luz_docs `JsonStoreQueryPatchUtil` single-doc path):
```json
[ {"op":"add","path":"/securityClassCodes","value":[]},
  {"op":"add","path":"/securityClassCodes/-","value":"DEV"} ]
```
Expected `["DEV"]`, got `[]`.

**Why:** The append-detector (`isElementAddedIntoArray`) checks whether the array exists *in the original document* — it can't see the array the earlier op created in the same patch. So the tail-append is mis-routed into `$set {"securityClassCodes.-":"DEV"}` (a literal dotted path with a `-` segment) which collides with the earlier `$set securityClassCodes:[]` and is dropped.

**Second, deeper reason:** a *classic* MongoDB update **cannot `$set` and `$push` the same field path** in one update. So even when the array IS detected, "reset-then-append the same array" is structurally impossible to express as `$set`+`$push`. (An aggregation-*pipeline* update works, because pipeline stages run sequentially — which is why luz_docs' `updateMany` path, built as a `$set`/`$concatArrays`+`$ifNull` pipeline, did NOT have this bug; only the classic single-doc path did.)

**Fix pattern:** Pre-normalize the patch — fold index/tail appends (`/X/-`, `/X/<idx>`) whose array `/X` was assigned an array value earlier in the *same* patch back into that assignment, computing the final array in Java and emitting one op. Leaves appends to pre-existing (not-reassigned) arrays untouched so the existing `$push` path is preserved.

Related: [[01 Overview - Folder Rename Cascade]] (same materialize/patch subsystem).

%% ai-graph-start %%

**Related notes:**
- [[RFC-6902 replace at array index expects a scalar element not an array]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[JSON-Patch remove-to-replace[] conversion skipped folderIds when another field's remove came first]]
- [[flattenArrayAddOps runs only in materialize branch]]
- [[luz-docs updateManyByFilter requires every targeted document to actually change]]

%% ai-graph-end %%