---
ai_hash: 1ee5134fda153da2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-17
entities: []
source: LUZ-154613 session 2026-06-17
status: seedling
tags:
- java
- collections
- null
- gotcha
title: Java List.of rejects null elements (NPE); use Arrays.asList for null-tolerant
  varargs
type: lesson
---

# Java List.of rejects null elements (NPE); use Arrays.asList for null-tolerant varargs

java.util.List.of(...) and List.of(array) REJECT null elements — throw NullPointerException on construction. java.util.Arrays.asList(...) tolerates nulls. So a null-tolerant varargs method must wrap with Arrays.asList, not List.of. Bit me in RoaringVisibleCount: a varargs overload forwarding null bitmaps used List.of(array) → NPE on the null element; switched to Arrays.asList and let the downstream filter(Objects::nonNull) drop them. General rule: List.of/Map.of are for known-non-null literals; for caller-supplied arrays/varargs that may contain null, use Arrays.asList (or filter first).

%% ai-graph-start %%

**Related notes:**
- [[JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]
- [[Java int-vs-Object-vararg overload call is ambiguous — pass explicit array to disambiguate]]
- [[empty-object-not-null sentinel defeats Optional.ofNullable null-guards]]
- [[javax.json single-arg getString throws NPE on missing key]]

%% ai-graph-end %%