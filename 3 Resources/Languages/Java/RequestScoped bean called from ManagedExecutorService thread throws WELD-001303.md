---
title: "RequestScoped bean called from ManagedExecutorService thread throws WELD-001303"
created: 2026-06-16
type: lesson
status: seedling
source: "LUZ-154613 session 2026-06-16"
tags: [java, cdi, weld, requestscoped, concurrency, gotcha, luz-docs]
---

# RequestScoped bean called from ManagedExecutorService thread throws WELD-001303

Offloading work to a `ManagedExecutorService` (CompletableFuture.supplyAsync(..., executor)) and calling a CDI `@RequestScoped` bean inside the task throws `org.jboss.weld.contexts.ContextNotActiveException: WELD-001303: No active contexts for scope type javax.enterprise.context.RequestScoped`. The request scope is bound to the original HTTP request thread; the executor worker threads have no active request context, so the @RequestScoped proxy can't resolve its instance.

Hit in luz-docs LUZ-154613: the parallelize fan-out count submits K sub-counts to a ManagedExecutorService; each calls `JsonStoreMongoService.countByFilter` (which is @RequestScoped) → every sub-count throws → the fan-out's fail-loud wrapper turns it into a 500. The single (K<=1) path works because it runs on the request thread. NOTE this also means the divide-conquer count never reached MongoDB, so the separate 'does the gateway coerce hex _id range bounds to ObjectId' question stays unverified until this is fixed.

Fixes:
1. Per-task: inject `javax.enterprise.context.control.RequestContextController`, call activate() at task start and deactivate() in finally. Gives each worker a FRESH request context — fine when the called method is fully argument-driven (countByFilter takes tenant/collection/query/token as params, no request state).
2. Propagate the SAME context: use MicroProfile Context Propagation (`ManagedExecutor`/`ThreadContext` with cleared/propagated CDI context) instead of the raw @Resource ManagedExecutorService.
3. Run on the request thread (no parallelism — defeats the purpose).

Lesson: before fanning RequestScoped/SessionScoped work onto a thread pool, plan context activation or propagation — it compiles fine and only fails at runtime under load.

## Related

- [[CompletableFuture parallel fan-out needs .toList() barrier before joining]]
- [[Parallelize visible-doc count by fan-out over _id ranges (luz-docs)]]
