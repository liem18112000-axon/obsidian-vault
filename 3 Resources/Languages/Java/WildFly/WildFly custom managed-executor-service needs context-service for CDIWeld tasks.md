---
title: "WildFly custom managed-executor-service needs context-service for CDI/Weld tasks"
created: 2026-06-18
type: lesson
status: seedling
source: "luz_docs LUZ-154613 2026-06-18"
tags: [wildfly, jakarta-ee, cdi, weld, concurrency, gotcha]
---

# WildFly custom managed-executor-service needs context-service for CDI/Weld tasks

A custom WildFly `<managed-executor-service>` whose tasks call CDI/Weld code (`RequestContextController.activate()`, `@ApplicationScoped` lookups, etc.) MUST declare a `context-service`, e.g. `context-service="default"`. Without it the executor threads have no Thread Context ClassLoader associated with the deployment, so Weld throws:

`IllegalStateException: WFLYWELD0039: Singleton not set for null. ... Thread Context ClassLoader that is not associated with the deployment.`

Context: added a dedicated `countFanout` managed-executor-service (64 threads) so the _shard count fan-out could run wider than the default pools ~6 threads, injected via `@Resource(lookup="java:jboss/ee/concurrency/executor/countFanout")`. The injection bound fine and tasks ran on the new pool, but each tasks `requestContextController.activate()` blew up with WFLYWELD0039 — because the new executor was defined without `context-service`, while WildFlys `default` executor has `context-service="default"`.

Gotchas:
- The bare `@Resource ManagedExecutorService` resolves to the `default` executor, which DOES have a context-service — that is why the original code worked and the custom pool did not.
- Other named executors in the same file omitted `context-service` and were fine ONLY because their tasks never touch Weld/request context.
- Fail-soft saved the user path: each task threw, the catch fell back to a single plain count (slow ~28s) and still returned 200 — the misconfigured pool degraded instead of 500ing. Good evidence for fail-soft over fail-loud.

Fix: `<managed-executor-service name="countFanout" core-threads="${env.COUNT_FANOUT_NUMBER_THREAD}" context-service="default" jndi-name="java:jboss/ee/concurrency/executor/countFanout"/>`

Related: [[Shard count fan-out: most of the win is at K=4, diminishing returns after]]

## Related

- [[Shard count fan-out: most of the win is at K=4, diminishing returns after]]
- [[ManagedExecutorService.execute loses CDI request context]]
