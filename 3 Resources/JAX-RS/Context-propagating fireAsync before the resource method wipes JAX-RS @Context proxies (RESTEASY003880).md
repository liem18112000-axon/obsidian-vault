---
title: "Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)"
created: 2026-06-15
type: lesson
status: seedling
source: "session 2026-06-15 luz-docs MaterializeResponseFilter"
tags: [jax-rs, resteasy, cdi, thread-local, gotcha, luz-docs]
---

# Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)

A JAX-RS `@Context` injectable (`UriInfo`, `Providers`, ...) is **not** a real object — it is a lazy proxy that resolves its backing data from a per-request **thread-local** at call time. A context-propagating CDI async event — `Event.fireAsync(evt, NotificationOptions.ofExecutor(managedExecutorService))` — captures and replays the request/security context onto a worker thread, and that capture **mutates the request threads thread-local context** as a side effect.

So if you fire such an event from a `ContainerRequestFilter` (which runs *before* the resource method), the resource methods lazy `@Context` proxies later resolve to nothing and the call fails **intermittently** with:

```
RESTEASY003880: Unable to find contextual data of type ...
```

Intermittent because it is race / thread-reuse dependent, not deterministic.

**Fix:** defer the async-event seeding to a `ContainerResponseFilter`. It runs *after* the resource method has already resolved and consumed its `@Context` params, so perturbing the thread-local afterward is harmless. The work still fires once per request, just later in the lifecycle.

**Evidence:** `luz_docs` `MaterializeResponseFilter` seeds the materialize batch-retry sweeps this way on purpose. Full write-up + sequence diagrams: `C:\Users\dvtliem\Kepler\luz_docs\docs\materialize-response-filter-rationale.md`.

General rule: never perturb request thread-locals before the resource method consumes its `@Context`.

## Related

- [[CDI async events]]
- [[luz-docs materialize]]
