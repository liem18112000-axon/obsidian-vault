---
ai_hash: 404f20675202c685
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: session 2026-06-01
status: seedling
tags:
- java
- jsonp
- luz-docs
- refactoring
title: JsonObjectUtil.convertJsonArrayToListString unwraps JsonString already
type: lesson
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

%% ai-graph-start %%

**Related notes:**
- [[userSecurityClassCodes param must be JSON array text not comma-separated]]
- [[JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]
- [[JSON-P createArrayBuilder(Collection) rejects built JsonValues]]

%% ai-graph-end %%