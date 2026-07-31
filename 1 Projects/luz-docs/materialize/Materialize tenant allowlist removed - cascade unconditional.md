---
title: "Materialize tenant allowlist removed - cascade unconditional"
created: 2026-07-21
type: observation
status: seedling
source: "session 2026-07-21 LUZ-156856"
tags: [luz-docs, materialize, feature-flag, LUZ-156856]
---

# Materialize tenant allowlist removed - cascade unconditional

On branch mt-receive/LUZ-156856-materialize-gate-migration-campaign (2026-07-21) the per-tenant materialize allowlist was deleted: `MaterializeAllowlist` bean, config property `luz.docs.tenants.use-materialized`, and `MaterializeFacade.shouldAllowedTenant` are gone. Write-path stamping (create/patch), cascades (rename/parent-change/subtree), and the response-filter retry sweeps now run **unconditionally for every tenant**.

The only remaining guard on the read path is the migration gate: `shouldUseMaterialized = gate.isMaterializationComplete(tenantId, token)` (campaign status L1, repo check L2 via @Fallback).

Removal pattern worth remembering: killing a feature-flag/allowlist means (1) unwrap every `if (allowed)` call-site guard, (2) delete the now-meaningless 'flag off' tests (e.g. recoverFolder_nonMaterializedTenant_firesNoMaterializeCascade), (3) sweep configs/helm for the dead property. Related: [[Weld subclass-based interception makes self-invocation intercepted]].

## Related

- [[Weld subclass-based interception makes self-invocation intercepted]]
