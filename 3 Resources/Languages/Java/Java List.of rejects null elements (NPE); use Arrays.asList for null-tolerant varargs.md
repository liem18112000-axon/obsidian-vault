---
title: "Java List.of rejects null elements (NPE); use Arrays.asList for null-tolerant varargs"
created: 2026-06-17
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-17"
tags: [java, collections, null, gotcha]
---

# Java List.of rejects null elements (NPE); use Arrays.asList for null-tolerant varargs

java.util.List.of(...) and List.of(array) REJECT null elements — throw NullPointerException on construction. java.util.Arrays.asList(...) tolerates nulls. So a null-tolerant varargs method must wrap with Arrays.asList, not List.of. Bit me in RoaringVisibleCount: a varargs overload forwarding null bitmaps used List.of(array) → NPE on the null element; switched to Arrays.asList and let the downstream filter(Objects::nonNull) drop them. General rule: List.of/Map.of are for known-non-null literals; for caller-supplied arrays/varargs that may contain null, use Arrays.asList (or filter first).
