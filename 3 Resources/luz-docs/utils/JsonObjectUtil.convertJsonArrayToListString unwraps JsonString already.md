---
title: "JsonObjectUtil.convertJsonArrayToListString unwraps JsonString already"
created: 2026-06-01
type: lesson
status: seedling
source: "session 2026-06-01"
tags: [java, jsonp, luz-docs, refactoring]
---

# JsonObjectUtil.convertJsonArrayToListString unwraps JsonString already

Before writing a custom stream that filters `JsonValue` for `JsonString.class::isInstance` then `.map(((JsonString) v)::getString)`, check `JsonObjectUtil.convertJsonArrayToListString(JsonArray, boolean withDoubleQuote)`.

With `withDoubleQuote = false` it iterates the array, picks out STRING entries, unwraps via `((JsonString) value).getString()`, and adds OBJECT entries as their JSON `toString()`. Other types (numbers, booleans) are silently dropped — same behaviour you'd hand-roll for a code-set extractor.

**Idiom:** to build a `Set<String>` from a comma-separated codes string:
```java
Set.copyOf(JsonObjectUtil.convertJsonArrayToListString(
        JsonObjectUtil.createArrayByString(codes), false));
```

Compare with the previous 4-line stream form — same output, less ceremony, no `JsonString` / `Collectors` imports.

**When it's wrong tool:** if you need NUMBER or BOOLEAN entries kept, write your own — the util drops them. Same if you need a different separator than what `createArrayByString` consumes.

## Related
[[Empty per-folder codes means public, not no-access]]
