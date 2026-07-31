---
ai_hash: dab53f069b2bbacd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-03
entities: []
source: luz_docs session 2026-06-03 LUZ-154159
status: seedling
tags:
- java
- cdi
- microprofile-fault-tolerance
- rollback
- compensation
- saga
- snapshot
- observability
- luz-docs
title: Preserve compensation state when rollback itself fails
type: lesson
---

# Preserve compensation state when rollback itself fails

When you build a snapshot+restore rollback for a non-transactional operation (Mongo updateMany, multi-doc cascade, distributed write fan-out), the rollback can fail too. If your `finally` blindly deletes the snapshot, you lose the only recovery handle on top of an already-inconsistent system.

**Rule:** never auto-delete a snapshot until you know it is no longer needed. A snapshot is no longer needed only when **either**:
1. The forward operation succeeded, **or**
2. The rollback completed.

If neither happened, the snapshot is the last record of pre-state — keep it.

**Implementation pattern (Java / MicroProfile Fault Tolerance):**

```java
void publicEntry(...) {
    var snapshot = repo.takeSnapshot(...);
    String snapshotId = JsonObjectUtil.getId(snapshot);
    boolean keepSnapshotForRecovery = false;
    try {
        self.attemptWithRetry(..., snapshotId);   // @Retry + @Fallback
    } catch (RollbackFailedException e) {
        keepSnapshotForRecovery = true;            // signal — leave the row on disk
        throw new BusinessException(...);           // standard 500 contract to caller
    } finally {
        if (!keepSnapshotForRecovery) repo.deleteSnapshot(snapshotId);
    }
}

void onAttemptExhausted(..., String snapshotId) {   // @Fallback
    try {
        repo.restoreFromSnapshot(snapshotId);
    } catch (Exception rollbackFailure) {
        LOGGER.log(Level.SEVERE, rollbackFailure, () -> "ROLLBACK FAILED snapshotId=" + snapshotId);
        throw new RollbackFailedException("rollback failed; snapshot preserved", snapshotId, rollbackFailure);
    }
    throw new BusinessException(...);
}
```

**Why the custom exception carries the snapshotId:**
- The outer `finally` cannot rely on a boolean stored before the throw, because the `@Fallback` method runs in the interceptor frame, not in the outer try.
- A typed marker exception is the cleanest way to signal "do not delete snapshot" up to the outer entry without coupling layers via flags.
- The outer catch reads the snapshotId from the exception so it can log / link to a sweeper job.

**Companion needs:**
- Background sweeper drains preserved snapshots later (`MaterializeRetryEvent`-style observer or cron). Without one, snapshots accumulate.
- Ops alerting on `SEVERE … ROLLBACK FAILED` log lines.
- Snapshot row should embed enough state to replay deterministically (here: per-doc pre-values + affected scope).

**Discovered while** wiring LUZ-154159 parent-folder-change cascade. Initial code threw `FolderException` immediately after `restore`, which silently lost the failure when restore itself threw, and outer `finally` deleted the snapshot regardless. Fixed by introducing `MaterializeRollbackFailedException` to signal "preserve for recovery" up to the outer entry.

**Related:**
- [[Snapshot for rollback must live outside retry boundary]] — sequencing constraint
- [[CDI self-invocation bypasses interceptor proxy]] — why `@Inject self` is required to route through the @Retry proxy

**Tags:** rollback, compensation, saga, snapshot, observability, java, microprofile-fault-tolerance, luz-docs

## Related

- [[Snapshot for rollback must live outside retry boundary]]
- [[CDI self-invocation bypasses interceptor proxy]]

%% ai-graph-start %%

**Related notes:**
- [[Snapshot for rollback must live outside retry boundary]]
- [[Cascade-marker pattern for crash-safe async retry]]
- [[luz_docs parent-change cascade recovers forward, not via snapshot rollback]]
- [[CDI self-invocation bypasses interceptor proxy]]
- [[luz_docs materialize passive retry via cascade markers]]

%% ai-graph-end %%