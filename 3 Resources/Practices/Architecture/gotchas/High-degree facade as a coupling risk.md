---
title: "High-degree facade as a coupling risk"
created: 2026-07-14
type: observation
status: seedling
source: "luz_docs MaterializeFacade graph review 2026-07-14"
tags: [architecture, coupling, facade, code-review, luz-docs]
---

# High-degree facade as a coupling risk

A facade with very high graph degree (30+ edges, and certainly 49+) is a hub: convenient in steady state, but a single point of breaking-change risk. Every incoming edge is a call site bound to its contract, so any signature or contract change breaks them all at once — there is no room for gradual migration. High coupling feels safe precisely while the abstraction is stable, which is why it accumulates.

Instance: luz_docs `MaterializeFacade` — 49 edges from `DocumentService`, `FolderService`, `DocumentCreatingService`, `FolderDeletingService` and tests. Threading one new parameter or materialization rule means touching all 49.

**Mitigations**
1. Split by use case once degree passes ~20–25 (`MaterializeOnCreateFacade`, `MaterializeOnDeleteFacade`, …).
2. Version the interface — deprecate, add, allow a transition window before removal.
3. Document what callers actually depend on (which sentinel fields, which guarantees).
4. Adapt rather than modify when one caller's needs diverge — wrap the facade.
5. Audit degree regularly (Graphify or similar) to catch hubs before they entrench.

**When high degree is fine:** entry points (controllers, message handlers), rarely-changing libraries (logging, utils), and integration points you already plan to version.

## Related

- [[Graphify for code reviews without source access]]
