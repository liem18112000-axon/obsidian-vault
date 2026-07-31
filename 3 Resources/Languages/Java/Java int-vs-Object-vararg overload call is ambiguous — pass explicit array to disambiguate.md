---
ai_hash: 33d3a4cf66f05c51
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: LUZ-154613 session 2026-06-16
status: seedling
tags:
- java
- overloading
- varargs
- gotcha
- compile-error
title: Java int-vs-Object-vararg overload call is ambiguous — pass explicit array
  to disambiguate
type: lesson
---

# Java int-vs-Object-vararg overload call is ambiguous — pass explicit array to disambiguate

When a class has overloaded constructors/methods where one takes `(... , int, Object...)` and another takes `(... , Object...)`, calling it with an `int` as the last explicit arg is AMBIGUOUS — javac cannot decide between binding the int to the `int` parameter (empty vararg) vs boxing it into the `Object...` vararg. Error: 'reference to X is ambiguous, both constructor ... match'.

Disambiguate by passing an explicit array for the varargs slot so the arity picks the intended overload:
`super(code, bundlePath, HttpStatus.SC_INTERNAL_SERVER_ERROR, new Object[0]);`
This forced luz-docs ParallelizeCountException to compile against LocalizedRuntimeException, which has both `(String,String,Object...)` and `(String,String,int,Object...)` ctors — exactly how the sibling DocumentException already calls it (`new Object[1]`). Seen LUZ-154613.

## Related

- [[1 Projects/luz-docs/count/optimize/Divide-and-Conquer Visible-Document Count]]

%% ai-graph-start %%

**Related notes:**
- [[Lombok one bad symbol cascades into hundreds of phantom missing-method errors]]
- [[JSON-P createArrayBuilder(Collection) rejects built JsonValues]]
- [[Java List.of rejects null elements (NPE); use Arrays.asList for null-tolerant varargs]]
- [[A delegating overload changes less code than widening an existing method signature]]
- [[Scrambled Java source shows as illegal-start-of-type errors mid-class]]

%% ai-graph-end %%