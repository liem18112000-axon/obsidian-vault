---
ai_hash: f5933eece28e279c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-13
entities:
- luz-docs
- RESTEASY003880
- UriInfo
- HTTP 500
- regression
- MaterializeRequestFilter
- async CDI events
- integration-test run
- POST /documents
- POST /folders
- org.jboss.resteasy.spi.LoggableFailure
- Providers
- server-side
- materialize-pattern merge
- '@RequestScoped'
- '@Provider'
- ContainerRequestFilter
- request thread
- resource method
- materialize-allowlisted tenant
- MaterializeFolderParentChangeService
- MaterializeFolderRenameService
- onRetry()
- fireAsync()
- NotificationOptions
- managedExecutorService
- context-propagating async CDI event
- RESTEasy
- RESTEasy thread-local context
- uriInfo.getAbsolutePathBuilder()
- FolderResource.createFolder
- DocumentResource.createDocument
- '@Context UriInfo proxy'
- ContextParameterInjector$GenericDelegatingProxy
- body (de)serialization
- DocumentUtil.getMetadata
- server stack trace
- SynchronousDispatcher
- async/FaultTolerance thread
- request-thread context
- async dispatch
- fix
- integration-test repo
- retry seeding
- ContainerResponseFilter
- MP context propagation
- user
- test repo
- server fix
- general principle
- JAX-RS
- JAX-RS @Context proxies
- downstream test symptom
- luz-docs IT create-document step
- base_metadata AttributeError
- Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies
  (RESTEASY003880)
- luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata
  AttributeError
source: dev IT run 2026-06-12 + server log analysis
status: seedling
tags:
- luz-docs
- materialize
- resteasy
- sprint-158
- regression
title: luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter
  firing async CDI events
type: observation
---

# luz-docs RESTEASY003880 UriInfo 500 regression traced to MaterializeRequestFilter firing async CDI events

On the 2026-06-12 dev integration-test run, the dominant failure cluster (~10-14 scenarios) was `POST /documents` and `POST /folders` intermittently returning HTTP 500 with `org.jboss.resteasy.spi.LoggableFailure: RESTEASY003880: Unable to find contextual data of type: javax.ws.rs.core.UriInfo` (and a sibling `...type: javax.ws.rs.ext.Providers`).

Root cause (server-side, luz_docs, introduced by the materialize-pattern merge): `MaterializeRequestFilter` is a `@RequestScoped @Provider ContainerRequestFilter` that runs on the request thread *before* the resource method, on every materialize-allowlisted tenant. It calls `MaterializeFolderParentChangeService.onRetry()` / `MaterializeFolderRenameService.onRetry()`, which do `retryEvent.fireAsync(event, NotificationOptions.ofExecutor(managedExecutorService))`. Firing that context-propagating async CDI event perturbs RESTEasys thread-local context, so when the resource method then calls `uriInfo.getAbsolutePathBuilder()` (`FolderResource.createFolder:95`, `DocumentResource.createDocument:275`) the lazy `@Context UriInfo` proxy (`ContextParameterInjector$GenericDelegatingProxy`) finds no context and throws RESTEASY003880. Same mechanism breaks `Providers` during body (de)serialization in `DocumentUtil.getMetadata`.

Confirmed from the server stack trace: the failure is on the normal `default task-N` request thread via `SynchronousDispatcher` — NOT an async/FaultTolerance thread — which is what proves the request-thread context was wiped mid-request rather than the work simply running off-thread.

Intermittent because the filter only fires for allowlisted tenants and the async dispatch races the request thread.

Fix lives in luz_docs (server), not the integration-test repo — e.g. move the retry seeding to a ContainerResponseFilter (after the resource method consumed UriInfo), snapshot/restore the RESTEasy context around the fireAsync, or decouple the seeding from MP context propagation. As of this session the user chose to fix the test repo only, so the server fix is pending.

General principle: [[3 Resources/Languages/Java/JAX-RS/Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]. Downstream test symptom: [[luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError]].

## Related

- [[3 Resources/Languages/Java/JAX-RS/Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]
- [[luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError]]

%% ai-graph-start %%

**Related notes:**
- [[Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)]]
- [[luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError]]
- [[ManagedExecutorService.execute loses CDI request context]]
- [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]]
- [[Snapshot for rollback must live outside retry boundary]]

**Relations:**
- regression — *affects* — luz-docs
- regression — *involves* — RESTEASY003880
- regression — *involves* — UriInfo
- regression — *involves* — HTTP 500
- regression — *is_traced_to* — MaterializeRequestFilter
- MaterializeRequestFilter — *fires* — async CDI events
- POST /documents — *returns* — HTTP 500
- POST /folders — *returns* — HTTP 500
- HTTP 500 — *includes* — org.jboss.resteasy.spi.LoggableFailure
- org.jboss.resteasy.spi.LoggableFailure — *is_type_of* — RESTEASY003880
- RESTEASY003880 — *mentions* — UriInfo
- RESTEASY003880 — *mentions* — Providers
- integration-test run — *observed* — HTTP 500
- MaterializeRequestFilter — *is_the_root_cause_of* — regression
- MaterializeRequestFilter — *is_located_in* — server-side
- MaterializeRequestFilter — *is_located_in* — luz-docs
- MaterializeRequestFilter — *is_introduced_by* — materialize-pattern merge
- MaterializeRequestFilter — *is_a* — ContainerRequestFilter
- MaterializeRequestFilter — *is_a* — @RequestScoped
- MaterializeRequestFilter — *is_a* — @Provider
- MaterializeRequestFilter — *runs_on* — request thread
- MaterializeRequestFilter — *runs_before* — resource method
- MaterializeRequestFilter — *calls* — MaterializeFolderParentChangeService.onRetry()
- MaterializeRequestFilter — *calls* — MaterializeFolderRenameService.onRetry()
- MaterializeFolderParentChangeService.onRetry() — *calls* — fireAsync()
- MaterializeFolderRenameService.onRetry() — *calls* — fireAsync()
- fireAsync() — *uses* — NotificationOptions
- NotificationOptions — *uses* — managedExecutorService
- context-propagating async CDI event — *perturbs* — RESTEasy thread-local context
- resource method — *calls* — uriInfo.getAbsolutePathBuilder()
- uriInfo.getAbsolutePathBuilder() — *is_located_in* — FolderResource.createFolder
- uriInfo.getAbsolutePathBuilder() — *is_located_in* — DocumentResource.createDocument
- @Context UriInfo proxy — *is_a* — ContextParameterInjector$GenericDelegatingProxy
- @Context UriInfo proxy — *finds_no* — RESTEasy thread-local context
- @Context UriInfo proxy — *throws* — RESTEASY003880
- context-propagating async CDI event — *breaks* — Providers
- context-propagating async CDI event — *affects* — body (de)serialization
- Providers — *is_used_in* — DocumentUtil.getMetadata
- server stack trace — *confirms_failure_on* — request thread
- request thread — *uses* — SynchronousDispatcher
- request-thread context — *was_wiped* — mid-request
- MaterializeRequestFilter — *fires_for* — materialize-allowlisted tenant
- async dispatch — *races* — request thread
- fix — *is_located_in* — luz-docs
- fix — *is_not_in* — integration-test repo
- fix — *involves* — move retry seeding to ContainerResponseFilter
- fix — *involves* — snapshot/restore RESTEasy context
- fix — *involves* — decouple retry seeding from MP context propagation
- ContainerResponseFilter — *runs_after* — resource method
- resource method — *consumes* — UriInfo
- user — *chose_to_fix* — test repo
- server fix — *is* — pending
- general principle — *describes* — Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)
- Context-propagating fireAsync — *wipes* — JAX-RS @Context proxies
- wiping JAX-RS @Context proxies — *causes* — RESTEASY003880
- downstream test symptom — *is* — luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError
- luz-docs IT create-document step — *has_symptom* — base_metadata AttributeError
- luz-docs IT create-document step — *swallows* — HTTP 500
- regression — *is_related_to* — Context-propagating fireAsync before the resource method wipes JAX-RS @Context proxies (RESTEASY003880)
- regression — *is_related_to* — luz-docs IT create-document step swallows server 500 and surfaces as cryptic base_metadata AttributeError

%% ai-graph-end %%