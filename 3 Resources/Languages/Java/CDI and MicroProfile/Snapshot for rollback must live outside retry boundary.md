---
title: "Snapshot for rollback must live outside retry boundary"
created: 2026-06-03
type: lesson
status: seedling
source: "luz_docs session 2026-06-03 LUZ-154159"
tags: [java, cdi, microprofile-fault-tolerance, retry, rollback, snapshot, gotcha, luz-docs]
---

# Snapshot for rollback must live outside retry boundary

When you wrap an idempotent-write operation with `@Retry` and want a snapshot-based rollback on retry-exhaustion, the snapshot capture **must happen outside the retried method**, not inside it. Otherwise every retry attempt re-captures the snapshot — and after the first failed attempt the snapshot includes the partial mutation that attempt left behind. A subsequent rollback then restores docs to that intermediate (wrong) state, defeating the purpose.

**Correct shape (two-method orchestration):**

```java
@Inject MyService self;

void publicEntry(...) {
    String snapshotId = repo.takeSnapshot(...);   // ONCE, before any retry
    try {
        self.retriedAction(..., snapshotId);      // proxy-routed → @Retry applies
    } finally {
        repo.deleteSnapshot(snapshotId);          // ALWAYS
    }
}

@Retry(retryOn = TransientException.class)
@Fallback(fallbackMethod = "onExhausted")
void retriedAction(..., String snapshotId) {
    boolean fullyApplied = repo.write(...);
    if (!fullyApplied) throw new TransientException(...); // partial = retry-eligible
}

void onExhausted(..., String snapshotId) {
    repo.restoreFromSnapshot(snapshotId);  // pre-cascade state, captured once
    throw new BusinessException(...);
}
```

**Why this works:**
- Snapshot reflects state *before any write* — restoring it puts the system back to a consistent pre-cascade point.
- The retried method is purely the write attempt. Re-running it on a partial result re-applies the same target state idempotently (`updateMany` with pipeline computes from current upstream state; already-correct docs no-op, stale ones get updated).
- Partial-apply (returned false) converted to thrown exception so `@Retry` actually fires — `@Retry` only triggers on throwable, not on `false` returns.

**Companion gotcha:** the two methods must be on the same CDI bean *but* the outer must invoke the inner through an `@Inject`-self-reference, else the proxy is bypassed and `@Retry` never fires. See [[CDI self-invocation bypasses interceptor proxy]].

**Discovered while** wiring `MaterializeFolderParentChangeService` for LUZ-154159 (luz_docs sprint-158). Initial attempt put `@Retry` on `onFolderParentChange` directly which took the snapshot itself; each retry re-captured snapshot mid-flight. Restructured to outer-orchestrator + inner-retried-action.

## Related

- [[CDI self-invocation bypasses interceptor proxy]]
- [[luz-docs materialize cascade]]
