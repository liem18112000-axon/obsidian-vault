---
ai_hash: 2d9b1b89150e2d1b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- Materialize tenant allowlist
- mt-receive/LUZ-156856-materialize-gate-migration-campaign
- MaterializeAllowlist
- luz.docs.tenants.use-materialized
- MaterializeFacade.shouldAllowedTenant
- Write-path stamping
- cascades
- rename
- parent-change
- subtree
- response-filter retry sweeps
- migration gate
- read path
- shouldUseMaterialized
- gate.isMaterializationComplete
- tenantId
- token
- L1
- L2
- Fallback
- Weld subclass-based interception makes self-invocation intercepted
source: session 2026-07-21 LUZ-156856
status: seedling
tags:
- luz-docs
- materialize
- feature-flag
- LUZ-156856
title: Materialize tenant allowlist removed - cascade unconditional
type: observation
---

# Materialize tenant allowlist removed - cascade unconditional

On branch mt-receive/LUZ-156856-materialize-gate-migration-campaign (2026-07-21) the per-tenant materialize allowlist was deleted: `MaterializeAllowlist` bean, config property `luz.docs.tenants.use-materialized`, and `MaterializeFacade.shouldAllowedTenant` are gone. Write-path stamping (create/patch), cascades (rename/parent-change/subtree), and the response-filter retry sweeps now run **unconditionally for every tenant**.

The only remaining guard on the read path is the migration gate: `shouldUseMaterialized = gate.isMaterializationComplete(tenantId, token)` (campaign status L1, repo check L2 via @Fallback).

Removal pattern worth remembering: killing a feature-flag/allowlist means (1) unwrap every `if (allowed)` call-site guard, (2) delete the now-meaningless 'flag off' tests (e.g. recoverFolder_nonMaterializedTenant_firesNoMaterializeCascade), (3) sweep configs/helm for the dead property. Related: [[Weld subclass-based interception makes self-invocation intercepted]].

## Related

- [[Weld subclass-based interception makes self-invocation intercepted]]

%% ai-graph-start %%

**Related notes:**
- [[Campaign flag now guards materialize write path (isAllowedTenant)]]
- [[luz-docs migration campaign per-tenant activation flow]]
- [[MaterializeGate migration check falls through to repo on missing campaign]]
- [[Materialize bulk PATCH fans out into N serial per-doc PATCH calls]]
- [[Luz gates must inject per-package Allowlist beans not static Campaign isAffectedFor]]

**Relations:**
- Materialize tenant allowlist — *removed* — cascade unconditional
- Materialize tenant allowlist — *deleted on branch* — mt-receive/LUZ-156856-materialize-gate-migration-campaign
- MaterializeAllowlist — *is a bean of* — Materialize tenant allowlist
- luz.docs.tenants.use-materialized — *is a config property of* — Materialize tenant allowlist
- MaterializeFacade.shouldAllowedTenant — *is a method of* — Materialize tenant allowlist
- MaterializeAllowlist — *was deleted* — Materialize tenant allowlist
- luz.docs.tenants.use-materialized — *was deleted* — Materialize tenant allowlist
- MaterializeFacade.shouldAllowedTenant — *was deleted* — Materialize tenant allowlist
- Write-path stamping — *runs unconditionally for* — every tenant
- cascades — *run unconditionally for* — every tenant
- rename — *is a type of* — cascades
- parent-change — *is a type of* — cascades
- subtree — *is a type of* — cascades
- response-filter retry sweeps — *run unconditionally for* — every tenant
- migration gate — *is a guard on* — read path
- shouldUseMaterialized — *is determined by* — gate.isMaterializationComplete
- gate.isMaterializationComplete — *uses* — tenantId
- gate.isMaterializationComplete — *uses* — token
- L1 — *is campaign status for* — migration gate
- L2 — *is repo check for* — migration gate
- L2 — *via* — Fallback
- Materialize tenant allowlist removed - cascade unconditional — *related to* — Weld subclass-based interception makes self-invocation intercepted

%% ai-graph-end %%