---
ai_hash: 7f4ff656b6b9be1c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities:
- luz_docs
- JsonStore
- change tracking
- client-layer wrapper
- CDI async events
- TrackingJsonStoreClient
- '@ApplicationScoped'
- '@Inject'
- '@RestClient'
- JsonStoreMongoClient
- JAX-RS interface
- JsonStoreChangeEvent
- Observer
- '@ObservesAsync'
- JPA entity-listener
- ChangeLogObserver
- DocMaterializeObserver
- FolderCascadeObserver
- audit
- _isPublic
- _effectiveSecurityClassCodes
- documents
- folderIds
- securityClassCode
- folders
- inheritedSecurityClassCodes
- parentFolderIds
- DocumentCreatingService
- DocumentService
- setCollectionFields
- updateOrRemoveMetadataFilterFields
- GroupService
- migration jobs
- stamping
- CDI decorator
- MicroProfile REST client proxies
- jsonstore-change-tracking-design.md
- .excalidraw diagram
- Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server
  resource
- CDI decorators and interceptors never fire on MicroProfile REST client proxies
source: session 2026-06-05, docs/jsonstore-change-tracking-design.md
status: seedling
tags:
- luz-docs
- materialize
- security-class
- design-decision
- cdi
title: luz_docs JsonStore change tracking via client-layer wrapper and CDI async events
type: model
---

# luz_docs JsonStore change tracking via client-layer wrapper and CDI async events

Design decision (2026-06-05, proposal for team review): luz_docs centralizes change detection for security-relevant fields at the JsonStore *client* layer instead of per-API service-layer stamping.

- `TrackingJsonStoreClient`: @ApplicationScoped wrapper holding the only `@Inject @RestClient JsonStoreMongoClient`; mirrors all client methods (does not implement the interface — see [[Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]]).
- On *One writes (insertOne/replaceOne/updateOne/deleteOne) to tracked collections: gate on payload touching a tracked field → projected pre-read for 'before' → delegate → on 2xx diff → `fireAsync(JsonStoreChangeEvent)`.
- Observers (`@ObservesAsync`) act as the JPA entity-listener analog: ChangeLogObserver (audit), DocMaterializeObserver (recompute _isPublic/_effectiveSecurityClassCodes, skip if write already stamped), FolderCascadeObserver (phase 2 subtree recompute).
- Tracked fields: documents → folderIds, securityClassCode; folders → securityClassCode, inheritedSecurityClassCodes, parentFolderIds.
- Why: today stamping lives in DocumentCreatingService/DocumentService call-sites; setCollectionFields, updateOrRemoveMetadataFilterFields, GroupService, migration jobs bypass it silently → stale sentinels.
- Accepted trade-offs: +1 projected getOne per tracked write; pre-read race (idempotent recompute converges); updateMany/deleteMany/findAndModify blind spot.
- A CDI decorator was rejected: [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]].

Full doc: luz_docs/docs/jsonstore-change-tracking-design.md + .excalidraw diagram.

## Related

- [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]]
- [[Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire method]]
- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[Intercept an MP REST client by implementing its interface - unqualified inject resolves the wrapper, RestClient qualifier is the bypass]]
- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[luz_docs change tracking phase 1 is scoped to the documents collection only]]

**Relations:**
- luz_docs — *centralizes* — change tracking
- change tracking — *for* — security-relevant fields
- change tracking — *at* — JsonStore client layer
- change tracking — *uses* — client-layer wrapper
- change tracking — *uses* — CDI async events
- TrackingJsonStoreClient — *is a* — client-layer wrapper
- TrackingJsonStoreClient — *is* — @ApplicationScoped
- TrackingJsonStoreClient — *holds* — JsonStoreMongoClient
- JsonStoreMongoClient — *is annotated with* — @Inject
- JsonStoreMongoClient — *is annotated with* — @RestClient
- TrackingJsonStoreClient — *mirrors* — client methods
- TrackingJsonStoreClient — *does not implement* — JAX-RS interface
- One writes — *fires* — JsonStoreChangeEvent
- Observer — *acts as* — JPA entity-listener
- Observer — *is annotated with* — @ObservesAsync
- ChangeLogObserver — *is an* — Observer
- ChangeLogObserver — *performs* — audit
- DocMaterializeObserver — *is an* — Observer
- DocMaterializeObserver — *recomputes* — _isPublic
- DocMaterializeObserver — *recomputes* — _effectiveSecurityClassCodes
- FolderCascadeObserver — *is an* — Observer
- FolderCascadeObserver — *performs* — phase 2 subtree recompute
- documents — *have tracked field* — folderIds
- documents — *have tracked field* — securityClassCode
- folders — *have tracked field* — securityClassCode
- folders — *have tracked field* — inheritedSecurityClassCodes
- folders — *have tracked field* — parentFolderIds
- stamping — *lives in* — DocumentCreatingService
- stamping — *lives in* — DocumentService
- setCollectionFields — *bypasses* — stamping
- updateOrRemoveMetadataFilterFields — *bypasses* — stamping
- GroupService — *bypasses* — stamping
- migration jobs — *bypasses* — stamping
- CDI decorator — *was rejected* — change tracking
- CDI decorators — *do not fire on* — MicroProfile REST client proxies
- jsonstore-change-tracking-design.md — *is a* — full documentation
- .excalidraw diagram — *is a* — full documentation
- Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource — *explains* — JAX-RS interface
- CDI decorators and interceptors never fire on MicroProfile REST client proxies — *explains rejection of* — CDI decorator

%% ai-graph-end %%