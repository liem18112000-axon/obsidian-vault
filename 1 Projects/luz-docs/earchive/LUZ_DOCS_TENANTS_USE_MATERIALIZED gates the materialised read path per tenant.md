---
title: "LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant"
created: 2026-06-11
type: reference
status: seedling
source: "session 2026-06-11"
tags: [luz-docs, earchive, materialize, configmap, gke]
---

# LUZ_DOCS_TENANTS_USE_MATERIALIZED gates the materialised read path per tenant

Env var `LUZ_DOCS_TENANTS_USE_MATERIALIZED` (config property `luz.docs.tenants.use-materialized`, see `MaterializeConstants.TENANT_USE_MATERIALIZED`) is the tenant allowlist for the eArchive materialised read path in luz-docs: comma-separated tenant ids, `*` enables all tenants, empty disables.

On dev GKE it lives in `luz-docs-env-configmap-<kustomize-hash>` (discover via `kubectl -n dev get statefulset luz-docs -o jsonpath` on envFrom). It is consumed via `envFrom`, so a ConfigMap patch alone does nothing — a `kubectl rollout restart statefulset/luz-docs` is required for pods to see the new value.

Caveat: the name hash suffix means the ConfigMap is kustomize-generated; an in-place patch can be reconciled away on the next `kubectl apply` from the overlay repo. For a durable change, also update `kubernetes-overlays` (the luz-kubernetes-add-env skill).

## Related

- [[1 Projects/luz-docs/earchive/luz_docs parent-change cascade recovers forward, not via snapshot rollback]]
