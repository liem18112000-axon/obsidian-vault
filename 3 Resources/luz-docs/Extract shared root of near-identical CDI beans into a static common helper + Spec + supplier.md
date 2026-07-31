---
title: "Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier"
created: 2026-07-31
type: howto
status: seedling
source: "session 2026-07-31"
tags: [luz-docs, java, cdi, refactoring, mockito, dry]
---

# Extract shared root of near-identical CDI beans into a static common helper + Spec + supplier

When two CDI @ApplicationScoped domain beans are near-identical (luz_docs: materialize vs parallelize gate / campaign-check), pull the shared **root** into a stateless `final` helper in `ch.klara.luz.docs.common` (`Gate`, `CampaignCheck`) exposing **static** methods. Parameterize the varying bits with a small `Spec` record (cacheKey, subject, TTLs, log noun) plus functional args: `BooleanSupplier` for the per-domain repo check (`isMaterialized`/`isSharded`) and `Supplier<CompletionStage<?>>` for the per-domain CDI event fire.

Keep the two beans as **thin delegators** that still hold their own fields — this is the load-bearing decision:

- `private DualCache cache = DualCache.of(...)` **must stay non-final** so Mockito `@InjectMocks` can inject the `@Mock DualCache`.
- `@Inject repo`, `@Inject MigrationCampaignService migration` stay as fields.
- CDI `Event<X>` + `ManagedExecutorService` stay domain-typed — an `@ObservesAsync` observer needs a concrete event type, so it can't be generified into common.

**Why static methods taking cache+migration as params** (instead of making the common class a bean that owns them): it preserves the existing `@InjectMocks` unit tests verbatim. Both gate + campaign-check test classes (24 tests) stayed green with **zero test edits and zero facade edits** — the mocks still inject into the same delegator fields, and the delegator passes them into the static helper.

Extract a shared cache-guard once to `common/Caches.safe(Supplier<String>)` so each helper doesn't re-copy its own `safeCache` (that copy is exactly what the reuse-gate flags).

Short-circuit gotcha: in the self-heal check, `!completed || actualCheck.getAsBoolean()` keeps the repo call lazy — a `BooleanSupplier` (not a pre-evaluated boolean) is required so the repo isn't hit when the campaign is not COMPLETED.

Related: [[DualCache namespace only selects the L1 bucket]].

## Related

- [[DualCache namespace only selects the L1 bucket]]
