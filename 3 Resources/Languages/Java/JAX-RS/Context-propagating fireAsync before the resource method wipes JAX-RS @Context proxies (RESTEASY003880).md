---
title: "Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)"
created: 2026-06-13
type: gotcha
status: seedling
tags: [jax-rs, jaxrs, resteasy, cdi, context-propagation, thread-local, java, gotcha, luz-docs]
entities: [RESTEASY003880, Event.fireAsync, NotificationOptions.ofExecutor, ManagedExecutorService, ContainerRequestFilter, ContainerResponseFilter, UriInfo, Providers, HttpHeaders, ResteasyContext, ContextParameterInjector$GenericDelegatingProxy]
---

# Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)

A JAX-RS `@Context` injectable (`UriInfo`, `Providers`, `HttpHeaders`, ...) is not a real object — it is a lazy proxy (RESTEasy: `ContextParameterInjector$GenericDelegatingProxy`) that resolves its backing data from a per-request **thread-local** (`ResteasyContext`) at call time.

Firing a context-propagating async CDI event — `event.fireAsync(payload, NotificationOptions.ofExecutor(managedExecutorService))` — or otherwise submitting to a Jakarta `ManagedExecutorService` **captures and replays the request context, mutating the request thread's own thread-local as a side effect.** Do that from a `ContainerRequestFilter` (i.e. before the resource method runs) and the resource method's `@Context` proxies later resolve to nothing:

```
RESTEASY003880: Unable to find contextual data of type: <type>
```

Intermittent — it depends on the propagation lifecycle racing the request thread.

**Fix:** move the async-firing work to a `ContainerResponseFilter`, which runs *after* the resource method has resolved and consumed its `@Context` params, so perturbing the thread-local afterward is harmless. The work still fires once per request. Alternatives: snapshot/restore the RESTEasy context around the submission, or capture only the primitives you need (tenantId, token) on the request thread and hand those to the executor, avoiding MP Context Propagation of the RESTEasy context entirely.

**Diagnostic tell:** the stack trace fails on a normal request thread (`default task-N` on WildFly/Undertow) through `SynchronousDispatcher`. If it were merely running off-thread you would see a `ThreadPoolExecutor` / `FaultToleranceInterceptor` frame instead.

**General rule:** never perturb request thread-locals before the resource method consumes its `@Context`.

Shipped shape in luz_docs: `MaterializeResponseFilter` (not `MaterializeRequestFilter`) seeds the materialize batch-retry sweeps. Write-up + sequence diagrams: `C:\Users\dvtliem\Kepler\luz_docs\docs\materialize-response-filter-rationale.md`.

## Related

- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]
- [[CDI async events]]
- [[luz-docs materialize]]
