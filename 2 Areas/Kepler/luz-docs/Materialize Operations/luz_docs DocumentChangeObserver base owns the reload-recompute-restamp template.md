---
title: "luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template"
created: 2026-06-05
type: model
status: budding
source: "DocumentChangeObserver refactor, session 2026-06-05"
tags: [luz-docs, change-tracking, cdi-events, template-method, design-decision]
---

# luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template

luz_docs `tracking.DocumentChangeObserver` (abstract CDI base) owns the whole reaction template for tracked document writes: inherited `@ObservesAsync` observer → `shouldSkip()` cheap pre-gate (default false) → reload the document from current truth via `JsonStoreMongoService.getDocumentById` → subclass `recompute(event, document)` returns the fields to stamp → write them back with `setDocumentFields` inside `ChangeTrackingSuppression.suppress()` (loop guard) → info log; any exception is logged SEVERE and never propagates to the write path. A concrete observer (e.g. `DocMaterializeObserver`) is reduced to two overrides: a skip-gate and a one-line recompute delegate.

Two consequences accepted knowingly (2026-06-05, with Liem):
1. The base is no longer behavior-agnostic — every subclass IS a reload-recompute-restamp reactor. A future observer with a different reaction shape (e.g. fan-out cascade, pure notification) will not fit and would need the template hook reopened.
2. Package cycle: `tracking` now injects `service.jsonstore.JsonStoreMongoService`, which itself imports `tracking.TrackingJsonStoreClient`. Java tolerates it; flagged as architectural debt.

Recompute-from-current-truth (not from the event diff) keeps replays idempotent — same principle as the cascade-marker retry design.

## Related

- [[CDI observer methods are inherited from superclasses but not across packages if package-private]]
- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]
- [[luz_docs materialize passive retry via cascade markers]]

## Update 2026-06-05: persist step is now an overridable hook

The re-stamp write was extracted from the template into `applyRecomputedFields(event, fields)` — a `protected` method whose default is `jsonStore.setDocumentFields(...)`. Subclasses with a different persistence shape override it; `DocMaterializeObserver` keeps the default untouched. Key design point: **the `ChangeTrackingSuppression.suppress()` wrap stays in the base around the hook call** — the loop guard is owned by the template and cannot be forgotten (or skipped) by an override.

## Update 2026-06-05: final hook shape — I/O-free base, two abstract hooks

The template settled as: `shouldSkip` (optional gate) -> abstract `loadDocument(event)` (null = gone, ignore) -> abstract **void** `recompute(event, document)` executed **inside base-owned `ChangeTrackingSuppression.suppress()`** -> info log; failures route to an overridable `onDocumentChangeFailed(event, e)` hook (default SEVERE log). The earlier separate `applyRecomputedFields` persist hook was merged INTO `recompute` (subclass computes and re-stamps in one method) — suppression now wraps the whole reaction, which is fine because suppression only gates writes and any tracked write inside the reaction must be suppressed anyway. With `loadDocument` abstracted, the base lost its `@Inject JsonStoreMongoService` entirely — `tracking` no longer depends on the service layer, breaking the tracking <-> service.jsonstore package cycle previously accepted as debt. `DocMaterializeObserver` owns the jsonstore calls in its `loadDocument`/`recompute` overrides.
