---
title: "Firing a context-propagating async CDI event from a JAX-RS ContainerRequestFilter can wipe RESTEasy @Context proxies"
created: 2026-06-13
type: lesson
status: seedling
source: "session 2026-06-12"
tags: [resteasy, jaxrs, cdi, context-propagation, java, gotcha]
---

# Firing a context-propagating async CDI event from a JAX-RS ContainerRequestFilter can wipe RESTEasy @Context proxies

A JAX-RS resource method that injects `@Context UriInfo` (or `Providers`, `HttpHeaders`, etc.) gets a **lazy thread-local-backed proxy** (RESTEasy: `ContextParameterInjector$GenericDelegatingProxy`). The proxy resolves the real object from `ResteasyContext` on the *current* thread only when a method is actually called on it. If the thread-local context map is cleared or swapped before that call, the proxy throws `RESTEASY003880: Unable to find contextual data of type: <type>`.

Gotcha: firing a **context-propagating async CDI event** — `event.fireAsync(payload, NotificationOptions.ofExecutor(managedExecutorService))` — or otherwise submitting to a Jakarta `ManagedExecutorService` from inside a `ContainerRequestFilter` (i.e. mid-request, before the resource method runs) can perturb/clear the RESTEasy thread-local context on the *originating request thread*. The subsequent resource-method `@Context` proxy resolution then fails with RESTEASY003880 even though everything is nominally on the request thread. It is intermittent because it depends on the propagation lifecycle racing the request thread.

Mitigations: (1) move the async-firing work to a `ContainerResponseFilter` (after the resource method has consumed its `@Context` params); (2) snapshot and restore the RESTEasy context around the async submission; (3) capture the primitive values you need (tenantId, token) on the request thread and hand only those to the executor, avoiding MP Context Propagation of the RESTEasy context.

Diagnostic tell: in the stack trace the failure is on a normal request thread (`default task-N` for WildFly/Undertow) through `SynchronousDispatcher` — if it were merely running off-thread you would see a `ThreadPoolExecutor` / `FaultToleranceInterceptor` frame instead.

Concrete instance: [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]].

## Related

- [[luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events]]
