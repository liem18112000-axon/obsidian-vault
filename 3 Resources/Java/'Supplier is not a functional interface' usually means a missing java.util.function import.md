---
title: "'Supplier is not a functional interface' usually means a missing java.util.function import"
created: 2026-09-04
type: lesson
status: seedling
source: "session 2026-09-04 — luz_jsonstore refactor"
tags: [java, gotcha, functional-interface, lambda, imports]
---

# 'Supplier is not a functional interface' usually means a missing java.util.function import

The Java compiler/IDE error **"Supplier<T> is not a functional interface"** (and the same for `Consumer`, `Function`, `Predicate`, etc.) is almost always caused by a **missing `import java.util.function.*` for that type** — not by any real defect in the interface or your lambda.

**Why the message is misleading:** when the import is absent, the bare name `Supplier` fails to resolve to `java.util.function.Supplier`. The symbol either stays unresolved or binds to some unrelated type in scope, so the compiler's target-typing check for the lambda / method reference fails. It then reports the confusing *"not a functional interface"* rather than *"cannot resolve symbol"*, plus a cascade of downstream errors like `Cannot resolve method 'accept()'` / `'get()'` / `'apply()'` at each call site.

**Fix:** add the correct import(s), e.g.
```java
import java.util.function.Supplier;
import java.util.function.Consumer;
```

**When I hit it:** refactoring `JsonStoreMongoDbServiceV2.toCollation` into a generic helper `mapIfPresent(Document, String, Supplier<T>, Consumer<T>)`. 22 diagnostics — all the per-line "not a functional interface" and "cannot resolve accept()/get()" errors — vanished the moment the two `java.util.function` imports were added. No code change was needed beyond the imports.

**Heuristic:** if a lambda/method-ref line reports "not a functional interface" for a well-known JDK functional type, check the import block *first* before suspecting your generics or the interface itself.

## Related

- [[Java]]
- [[Java functional interfaces]]
