---
title: "JsonValue.NULL is a non-null Java object so Objects::nonNull does not drop JSON null elements"
created: 2026-06-21
type: gotcha
status: seedling
source: "session 2026-06-21 luz-docs delete-folder review"
tags: [java, json, javax-json, gotcha]
---

# JsonValue.NULL is a non-null Java object so Objects::nonNull does not drop JSON null elements

When you parse a JSON array containing a literal `null` element (e.g. via javax.json `JsonReader.readArray()`), that element becomes the singleton `JsonValue.NULL` — which is a real, non-null Java object. So `stream().filter(Objects::nonNull)` does NOT remove it; the element survives and the next `.map(JsonValue::asJsonObject)` throws `ClassCastException`.

**Guard correctly**: `value.getValueType() != JsonValue.ValueType.NULL` (or `!value.equals(JsonValue.NULL)`). A parsed JsonArray never contains Java `null`, so `Objects::nonNull` on it is effectively dead code — it guards a null that can't occur while letting the JSON null through.

Seen in luz_docs `FolderUtil.forEachDocumentPage` while reviewing the folder-delete discovery phase.

## Related
[[Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple counts]]

## Related

- [[Offset-paging loop with while(offset % pageSize == 0) infinite-loops on exact-multiple counts]]
