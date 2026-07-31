---
ai_hash: 02eb8f0726494449
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-01
entities: []
source: session 2026-06-01
status: seedling
tags:
- java
- records
- refactoring
- testing
- gotcha
title: Adding a field to a Java record breaks all factory and constructor calls in
  tests
type: lesson
---

# Adding a field to a Java record breaks all factory and constructor calls in tests

Adding a component to a Java 17 record breaks every `.of(...)` factory and every `new Record(...)` ctor call in the test suite — the canonical and any derived signatures are fixed by component count, and the compiler rejects every old call site.

**When it bites:** evolving a sentinel-bearing record like `MaterializeState` from 3 to 4 components.

**Why sed isn't enough:** the naive pattern `Record\.of\([^)]*\)` terminates early on the first `)`, which is usually the inner `.build()` paren. Use a balanced-paren scan instead — for each occurrence, walk forward counting `(`/`)` until depth hits zero, then insert the new arg before that close-paren.

**Tactic (Node one-liner):** read file, scan for the call needle, append `, <newArg>` at the matching close. Run for `Record.of(` and `new Record(` separately. Works for any literal positional default (`List.of()`, `null`, etc.).

Companion: bump any assertion that counts derived outputs (e.g. `appendAsPatchOps` op count) by the same delta — these break at runtime, not compile time, so they're easy to miss.

## Related
[[MaterializeState]]

%% ai-graph-start %%

**Related notes:**
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]
- [[Run the full affected test package locally, not a hand-picked subset]]
- [[Gate behavior changes must update tests asserting old fallthrough in the same commit]]
- [[Materialize appendAsPatchOps uses RFC-6902 replace for sentinel fields]]
- [[New collaborator call NPEs old @InjectMocks tests]]

%% ai-graph-end %%