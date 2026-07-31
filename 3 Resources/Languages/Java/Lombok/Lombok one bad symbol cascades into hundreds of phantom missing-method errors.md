---
title: "Lombok: one bad symbol cascades into hundreds of phantom missing-method errors"
created: 2026-06-27
type: lesson
status: seedling
source: "session 2026-06-27"
tags: [java, lombok, build, gotcha]
---

# Lombok: one bad symbol cascades into hundreds of phantom missing-method errors

In a Lombok project, a **single** genuine compile error (one unresolved symbol — e.g. an orphan `import static …MIN_TERM_LENGTH` after the constant was deleted) can make `javac` abort annotation processing for the whole module. Lombok then never generates `@Getter`/`@Setter`/`@Builder` methods, so the compiler reports **hundreds of cascade errors** like `cannot find symbol method builder()/getX()/getTenantId()` across many unrelated files (records, event POJOs, response builders).

**Lesson:** do not chase the cascade. Find the FIRST real error that is NOT a missing Lombok-generated method (a deleted constant, a renamed class, a bad import), fix that one, and recompile — the hundreds of phantom errors vanish together.

Seen 2026-06-27 in luz_docs: removing `MIN_TERM_LENGTH` from `NgramConstants` left an orphan static import in `TrigramQuery`; that one line produced a full-module cascade incl. `DocumentEventFail.builder()`, `AnalyzeServiceResponseEvent.getDocumentId()`, `UpdateSecurityClassesResponseBuilder`. Deleting the orphan import → clean build.
