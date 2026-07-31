---
ai_hash: 0d4f4b2c6379962d
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities:
- LUZ_DOCS_TENANTS_USE_MATERIALIZED
- materialised read path
- tenant
- luz.docs.tenants.use-materialized
- MaterializeConstants.TENANT_USE_MATERIALIZED
- eArchive
- luz-docs
- dev GKE
- luz-docs-env-configmap-<kustomize-hash>
- kubectl
- statefulset luz-docs
- ConfigMap
- kubectl rollout restart statefulset/luz-docs
- kustomize
- kubernetes-overlays
- luz-kubernetes-add-env skill
- 1 Projects/luz-docs/earchive/luz_docs parent-change cascade recovers forward, not
  via snapshot rollback
- envFrom
- pods
- name hash suffix
- in-place patch
- kubectl apply
- durable change
source: session 2026-06-11
status: seedling
tags:
- luz-docs
- earchive
- materialize
- configmap
- gke
title: LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant
type: reference
---

# LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant

Env var `LUZ_DOCS_TENANTS_USE_MATERIALIZED` (config property `luz.docs.tenants.use-materialized`, see `MaterializeConstants.TENANT_USE_MATERIALIZED`) is the tenant allowlist for the eArchive materialised read path in luz-docs: comma-separated tenant ids, `*` enables all tenants, empty disables.

On dev GKE it lives in `luz-docs-env-configmap-<kustomize-hash>` (discover via `kubectl -n dev get statefulset luz-docs -o jsonpath` on envFrom). It is consumed via `envFrom`, so a ConfigMap patch alone does nothing — a `kubectl rollout restart statefulset/luz-docs` is required for pods to see the new value.

Caveat: the name hash suffix means the ConfigMap is kustomize-generated; an in-place patch can be reconciled away on the next `kubectl apply` from the overlay repo. For a durable change, also update `kubernetes-overlays` (the luz-kubernetes-add-env skill).

## Related

- [[1 Projects/luz-docs/earchive/luz_docs parent-change cascade recovers forward, not via snapshot rollback]]

%% ai-graph-start %%

**Related notes:**
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]
- [[luz-docs migration runs on a cron window via LUZ_DOCS_MIGRATION_PROCESSING_CRON]]
- [[luz-docs migration campaign per-tenant activation flow]]
- [[Materialize tenant allowlist removed - cascade unconditional]]
- [[Campaign-gate template cache then campaign status L1 then repository L2]]

**Relations:**
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *gates* — materialised read path
- materialised read path — *is per* — tenant
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *is an env var* — LUZ_DOCS_TENANTS_USE_MATERIALIZED
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *has config property* — luz.docs.tenants.use-materialized
- luz.docs.tenants.use-materialized — *is defined in* — MaterializeConstants.TENANT_USE_MATERIALIZED
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *is tenant allowlist for* — eArchive materialised read path
- eArchive materialised read path — *is in* — luz-docs
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *lives in* — luz-docs-env-configmap-<kustomize-hash>
- luz-docs-env-configmap-<kustomize-hash> — *is on* — dev GKE
- luz-docs-env-configmap-<kustomize-hash> — *discovered via* — kubectl
- kubectl — *queries* — statefulset luz-docs
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *is consumed via* — envFrom
- ConfigMap patch — *requires* — kubectl rollout restart statefulset/luz-docs
- kubectl rollout restart statefulset/luz-docs — *is required for pods to see* — new value
- ConfigMap — *is* — kustomize-generated
- kustomize-generated ConfigMap — *has* — name hash suffix
- in-place patch — *can be reconciled by* — kubectl apply
- durable change — *requires update to* — kubernetes-overlays
- kubernetes-overlays — *uses* — luz-kubernetes-add-env skill
- LUZ_DOCS_TENANTS_USE_MATERIALIZED — *is related to* — 1 Projects/luz-docs/earchive/luz_docs parent-change cascade recovers forward, not via snapshot rollback

%% ai-graph-end %%