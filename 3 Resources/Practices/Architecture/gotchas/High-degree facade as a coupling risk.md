---
title: "High-degree facade as a coupling risk"
created: 2026-07-14
type: observation
status: seedling
tags: [architecture, coupling, facade, code-review]
---

# High-degree facade as a coupling risk

A facade with very high graph degree (30+ edges, especially 49+) is a hub—efficient and convenient during normal operation, but a single point of breaking-change risk.

**The problem:** If the facade has 49 incoming edges (from DocumentService, FolderService, DocumentCreatingService, tests, etc.), any API change to the facade signature or contract breaks ALL 49 callers simultaneously. No room for gradual migration.

**Why this happens:**
- The facade is convenient; many layers import and call it because it abstracts complexity.
- The abstraction is stable initially, so high coupling feels safe.
- But when requirements shift and the facade must change, you're forced to coordinate across all callers at once.

**Real example — MaterializeFacade:**
- Used by DocumentService, FolderService, DocumentCreatingService, FolderDeletingService, tests.
- 49 edges means 49 call sites that depend on its contract.
- If you discover a new materialization rule or need to thread a new parameter, every caller must be updated.

**Mitigation strategies:**
1. **Break into smaller, focused facades** if degree grows beyond ~20–25. Split by use case: MaterializeOnCreateFacade, MaterializeOnDeleteFacade, etc. Each has fewer callers.
2. **Version the interface** — deprecate old methods, add new ones, allow a transition period before removing.
3. **Document the contract carefully** — ensure all callers understand what they depend on (which sentinel fields, which guarantees).
4. **Use adapter patterns** — if a caller's needs diverge, wrap the facade instead of modifying it.
5. **Audit degree regularly** — use Graphify or similar tools to catch high-degree facades early, before they become entrenched.

**When high degree is OK:**
- Entry points (controllers, message handlers) naturally have high degree.
- Stable libraries that rarely change (e.g., logging, utils).
- Strategic integration points you plan to version upfront.

## Related

- [[Graphify for code reviews without source access]]
