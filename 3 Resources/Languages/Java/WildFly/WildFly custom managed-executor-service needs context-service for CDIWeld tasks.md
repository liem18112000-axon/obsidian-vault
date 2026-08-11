---
ai_hash: ffa897b0e799945f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: luz_docs LUZ-154613 2026-06-18
status: seedling
tags:
- wildfly
- jakarta-ee
- cdi
- weld
- concurrency
- gotcha
title: WildFly custom managed-executor-service needs context-service for CDI/Weld
  tasks
type: lesson
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

Related: [[3 Resources/Practices/Performance/Shard count fan-out most of the win is at K=4, diminishing returns after]]

## Related

- [[3 Resources/Practices/Performance/Shard count fan-out most of the win is at K=4, diminishing returns after]]
- [[ManagedExecutorService.execute loses CDI request context]]

%% ai-graph-start %%

**Related notes:**
- [[ManagedExecutorService.execute loses CDI request context]]
- [[ManagedExecutorService needs @Resource not @Inject in WildFlyWeld]]
- [[Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]
- [[CDI self-invocation bypasses interceptor proxy]]
- [[Widening fan-out threads doesn't help once MongoDB is the count bottleneck]]

%% ai-graph-end %%