---
ai_hash: ac1a3d89d0edbb9e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-27
entities: []
source: session 2026-06-27
status: seedling
tags:
- java
- lombok
- build
- gotcha
title: 'Lombok: one bad symbol cascades into hundreds of phantom missing-method errors'
type: lesson
---

# Lombok: one bad symbol cascades into hundreds of phantom missing-method errors

In a Lombok project, a **single** genuine compile error (one unresolved symbol — e.g. an orphan `import static …MIN_TERM_LENGTH` after the constant was deleted) can make `javac` abort annotation processing for the whole module. Lombok then never generates `@Getter`/`@Setter`/`@Builder` methods, so the compiler reports **hundreds of cascade errors** like `cannot find symbol method builder()/getX()/getTenantId()` across many unrelated files (records, event POJOs, response builders).

**Lesson:** do not chase the cascade. Find the FIRST real error that is NOT a missing Lombok-generated method (a deleted constant, a renamed class, a bad import), fix that one, and recompile — the hundreds of phantom errors vanish together.

Seen 2026-06-27 in luz_docs: removing `MIN_TERM_LENGTH` from `NgramConstants` left an orphan static import in `TrigramQuery`; that one line produced a full-module cascade incl. `DocumentEventFail.builder()`, `AnalyzeServiceResponseEvent.getDocumentId()`, `UpdateSecurityClassesResponseBuilder`. Deleting the orphan import → clean build.

%% ai-graph-start %%

**Related notes:**
- [[Verify wildcard-to-explicit import cleanup by compiling]]
- [[Scrambled Java source shows as illegal-start-of-type errors mid-class]]
- [[Java int-vs-Object-vararg overload call is ambiguous — pass explicit array to disambiguate]]
- [[A refactor that removes a method must grep tests for its name before merging]]
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]

%% ai-graph-end %%