---
title: "luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events"
created: 2026-06-13
type: observation
status: seedling
source: "dev IT run 2026-06-12 + server log analysis"
tags: [luz-docs, materialize, resteasy, sprint-158, regression]
---

# luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events

On the 2026-06-12 dev integration-test run, the dominant failure cluster (~10-14 scenarios) was `POST /documents` and `POST /folders` intermittently returning HTTP 500 with `org.jboss.resteasy.spi.LoggableFailure: RESTEASY003880: Unable to find contextual data of type: javax.ws.rs.core.UriInfo` (and a sibling `...type: javax.ws.rs.ext.Providers`).

Root cause (server-side, luz_docs, introduced by the materialize-pattern merge): `MaterializeRequestFilter` is a `@RequestScoped @Provider ContainerRequestFilter` that runs on the request thread *before* the resource method, on every materialize-allowlisted tenant. It calls `MaterializeFolderParentChangeService.onRetry()` / `MaterializeFolderRenameService.onRetry()`, which do `retryEvent.fireAsync(event, NotificationOptions.ofExecutor(managedExecutorService))`. Firing that context-propagating async CDI event perturbs RESTEasys thread-local context, so when the resource method then calls `uriInfo.getAbsolutePathBuilder()` (`FolderResource.createFolder:95`, `DocumentResource.createDocument:275`) the lazy `@Context UriInfo` proxy (`ContextParameterInjector$GenericDelegatingProxy`) finds no context and throws RESTEASY003880. Same mechanism breaks `Providers` during body (de)serialization in `DocumentUtil.getMetadata`.

Confirmed from the server stack trace: the failure is on the normal `default task-N` request thread via `SynchronousDispatcher` — NOT an async/FaultTolerance thread — which is what proves the request-thread context was wiped mid-request rather than the work simply running off-thread.

Intermittent because the filter only fires for allowlisted tenants and the async dispatch races the request thread.

Fix lives in luz_docs (server), not the integration-test repo — e.g. move the retry seeding to a ContainerResponseFilter (after the resource method consumed UriInfo), snapshot/restore the RESTEasy context around the fireAsync, or decouple the seeding from MP context propagation. As of this session the user chose to fix the test repo only, so the server fix is pending.

General principle: [[Firing a context-propagating async CDI event from a JAX-RS ContainerRequestFilter can wipe RESTEasy @Context proxies]]. Downstream test symptom: [[luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError]].

## Related

- [[Firing a context-propagating async CDI event from a JAX-RS ContainerRequestFilter can wipe RESTEasy @Context proxies]]
- [[luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError]]
