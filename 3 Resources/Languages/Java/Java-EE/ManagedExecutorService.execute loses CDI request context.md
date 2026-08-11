---
ai_hash: bb9cd326e89ead0f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
status: seedling
tags:
- java
- cdi
- weld
- java-ee
- requestscoped
- concurrency
- async
- gotcha
- luz-docs
title: ManagedExecutorService.execute loses CDI request context
type: gotcha
---

# ManagedExecutorService.execute loses CDI request context

Submitting work to a bare `ManagedExecutorService.execute()` / `CompletableFuture.supplyAsync(..., executor)` gives the task **no active CDI request context**. The request scope is bound to the original HTTP request thread; the first call into a `@RequestScoped` bean from a worker thread throws:

```
org.jboss.weld.contexts.ContextNotActiveException: WELD-001303:
No active contexts for scope type javax.enterprise.context.RequestScoped
```

Compiles fine, only fails at runtime — and if the caller swallows RuntimeExceptions (catch-and-log guard helpers), the failure is **silent**: the async job "runs", produces a wrong default, and caches/persists it.

## Fixes

1. **CDI async event (preferred, same executor):** `Event.fireAsync(payload, NotificationOptions.ofExecutor(executor))` observed with `@ObservesAsync`. CDI 2.0 §6.7 requires the container to activate a request context for async observer notification, so `@RequestScoped` beans work inside the observer. (Shipped LUZ-157705 pattern.)
2. **Activate per task:** inject `javax.enterprise.context.control.RequestContextController`, `activate()` at task start, `deactivate()` in `finally`. Gives each worker a FRESH context — fine when the called method is fully argument-driven (no request state).
3. **Propagate the same context:** MicroProfile Context Propagation (`ManagedExecutor`/`ThreadContext`) instead of the raw `@Resource ManagedExecutorService`.

## Why it's sneaky

Fully `@ApplicationScoped` bean chains work off-thread, so tests exercising only those paths pass. In luz_docs, `MigrationCampaignService` (app-scoped REST client) works from a raw executor but `JsonStoreMongoService` (`@RequestScoped`) does not — LUZ-154613's parallelize fan-out count submitted K sub-counts calling `JsonStoreMongoService.countByFilter`, every one threw, and the fail-loud wrapper turned it into a 500 (the K<=1 path worked because it ran on the request thread). A stale-while-revalidate gate would instead have silently cached INCOMPLETE forever for exactly the tenants relying on the count fall-through.

**Rule:** before fanning `@RequestScoped`/`@SessionScoped` work onto a thread pool, plan context activation or propagation — never raw `executor.execute`.

## Related

- [[WildFly custom managed-executor-service needs context-service for CDIWeld tasks]]
- [[CompletableFuture parallel fan-out needs .toList() barrier before joining]]
- [[1 Projects/luz-docs/count/optimize/Divide-and-Conquer Visible-Document Count]]
- [[Per-pod single-flight kills cache stampede without semantic change]]

%% ai-graph-start %%

**Related notes:**
- [[WildFly custom managed-executor-service needs context-service for CDIWeld tasks]]
- [[Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]
- [[ManagedExecutorService needs @Resource not @Inject in WildFlyWeld]]
- [[CDI self-invocation bypasses interceptor proxy]]
- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]

%% ai-graph-end %%