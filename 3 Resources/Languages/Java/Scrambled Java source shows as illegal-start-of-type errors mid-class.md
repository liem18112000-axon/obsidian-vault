---
ai_hash: 134ba83b20b9d633
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- java
- javac
- gotcha
- merge-conflict
- luz-docs
title: Scrambled Java source shows as illegal-start-of-type errors mid-class
type: lesson
---

# Scrambled Java source shows as illegal-start-of-type errors mid-class

javac errors like `illegal start of type`, `<identifier> expected`, `invalid method declaration; return type required` clustered mid-class — at lines that look like intact fluent-builder fragments (`.add(...)`, `return Json.createObjectBuilder()`) — usually mean the source file got *scrambled*: lines from two methods interleaved by a botched edit or merge, not a real syntax mistake in newly written code.

The tell: each individual fragment is syntactically fine on its own; only the line ordering is broken. A method signature appears *below* its own `return` statement, or a builder chain tail floats after an unrelated method.

Fix by **reconstructing statement order from the surviving fragments** rather than rewriting the method from memory — all the original lines are present, just shuffled, so reassembly preserves the exact intended logic.

Seen in luz_docs `MaterializeCascadeMarker.java`: the `parentChangeRetryMarker` body was split around two other methods; reassembling the builder chain restored compile.

%% ai-graph-start %%

**Related notes:**
- [[Verify wildcard-to-explicit import cleanup by compiling]]
- [[Lombok one bad symbol cascades into hundreds of phantom missing-method errors]]
- [[Java int-vs-Object-vararg overload call is ambiguous — pass explicit array to disambiguate]]
- [[Adding a field to a Java record breaks all factory and constructor calls in tests]]
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]

%% ai-graph-end %%