---
title: "Semaphore permit leak when risky code sits between acquire and try"
created: 2026-07-10
type: lesson
status: seedling
source: "luz_docs MaterializeMigrationExecutor.java review, 2026-07-10"
tags: [java, concurrency, semaphore, gotcha, luz-docs]
---

# Semaphore permit leak when risky code sits between acquire and try

A `Semaphore.acquire()` (or `acquireUninterruptibly()`) must be immediately followed by the `try` block whose `finally` calls `release()` — any statement placed between `acquire()` and that `try` is a permit-leak risk, because if it throws, `release()` never runs and the permit is gone forever.

**Concrete case** (`MaterializeMigrationExecutor.java`, luz_docs): code did

```java
BURST.acquireUninterruptibly();
var document = x.asJsonObject(); // unchecked cast, can throw ClassCastException
try {
    materialize(...);
} finally {
    BURST.release();
}
```

`x.asJsonObject()` sat *before* the `try`, so a `ClassCastException` there skips `release()` entirely. `BURST` was a `static Semaphore(3)` shared JVM-wide across every migration run, so three such throws permanently exhaust it — every future `acquireUninterruptibly()` call blocks forever, hanging all migrations until the pod restarts.

A second-order effect: the exception also escaped the inner `catch` (which only wrapped the `materialize()` call), propagated through `CompletableFuture.runAsync`, surfaced as a `CompletionException` at `.join()`, and aborted the whole batch-processing loop — losing the running `failed`/`total` counters and never setting a campaign completion status.

**Fix**: move the risky statement inside the `try`, with a `finally` that always releases regardless of what throws in between.

## Related
[[Static shared resources leak state across unrelated call sites]]
