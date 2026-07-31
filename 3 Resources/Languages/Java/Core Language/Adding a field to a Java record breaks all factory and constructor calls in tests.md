---
title: "Adding a field to a Java record breaks all factory and constructor calls in tests"
created: 2026-06-01
type: lesson
status: seedling
source: "session 2026-06-01"
tags: [java, records, refactoring, testing, gotcha]
---

# Adding a field to a Java record breaks all factory and constructor calls in tests

Adding a component to a Java 17 record breaks every `.of(...)` factory and every `new Record(...)` ctor call in the test suite — the canonical and any derived signatures are fixed by component count, and the compiler rejects every old call site.

**When it bites:** evolving a sentinel-bearing record like `MaterializeState` from 3 to 4 components.

**Why sed isn't enough:** the naive pattern `Record\.of\([^)]*\)` terminates early on the first `)`, which is usually the inner `.build()` paren. Use a balanced-paren scan instead — for each occurrence, walk forward counting `(`/`)` until depth hits zero, then insert the new arg before that close-paren.

**Tactic (Node one-liner):** read file, scan for the call needle, append `, <newArg>` at the matching close. Run for `Record.of(` and `new Record(` separately. Works for any literal positional default (`List.of()`, `null`, etc.).

Companion: bump any assertion that counts derived outputs (e.g. `appendAsPatchOps` op count) by the same delta — these break at runtime, not compile time, so they're easy to miss.

## Related
[[MaterializeState]]
