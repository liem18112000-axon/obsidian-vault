---
title: "luz_docs JsonStore change tracking via client-layer wrapper and CDI async events"
created: 2026-06-05
type: model
status: seedling
source: "session 2026-06-05, docs/jsonstore-change-tracking-design.md"
tags: [luz-docs, materialize, security-class, design-decision, cdi]
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
