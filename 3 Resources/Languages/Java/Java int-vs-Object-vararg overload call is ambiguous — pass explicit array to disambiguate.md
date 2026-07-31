---
title: "Java int-vs-Object-vararg overload call is ambiguous — pass explicit array to disambiguate"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [java, overloading, varargs, gotcha, compile-error]
---

# Java int-vs-Object-vararg overload call is ambiguous — pass explicit array to disambiguate

When a class has overloaded constructors/methods where one takes `(... , int, Object...)` and another takes `(... , Object...)`, calling it with an `int` as the last explicit arg is AMBIGUOUS — javac cannot decide between binding the int to the `int` parameter (empty vararg) vs boxing it into the `Object...` vararg. Error: 'reference to X is ambiguous, both constructor ... match'.

Disambiguate by passing an explicit array for the varargs slot so the arity picks the intended overload:
`super(code, bundlePath, HttpStatus.SC_INTERNAL_SERVER_ERROR, new Object[0]);`
This forced luz-docs ParallelizeCountException to compile against LocalizedRuntimeException, which has both `(String,String,Object...)` and `(String,String,int,Object...)` ctors — exactly how the sibling DocumentException already calls it (`new Object[1]`). Seen LUZ-154613.

## Related

- [[Parallelize visible-doc count by fan-out over _id ranges (luz-docs)]]
