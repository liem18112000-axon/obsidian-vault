---
title: "luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template"
created: 2026-06-05
type: model
status: budding
source: "DocumentChangeObserver refactor, session 2026-06-05"
tags: [luz-docs, change-tracking, cdi-events, template-method, design-decision]
---

# luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template

luz_docs `tracking.DocumentChangeObserver` (abstract CDI base) owns the entire reaction template for tracked document writes, so a concrete observer (e.g. `DocMaterializeObserver`) only fills in hooks.

Final shape (after the 2026-06-05 iterations):

1. inherited `@ObservesAsync` observer method
2. `shouldSkip(event)` — optional cheap pre-gate, default false
3. abstract `loadDocument(event)` — returns null when the doc is gone (ignore)
4. abstract **void** `recompute(event, document)` — subclass computes *and* re-stamps, executed inside base-owned `ChangeTrackingSuppression.suppress()`
5. info log; failures route to overridable `onDocumentChangeFailed(event, e)` (default SEVERE log), never propagating to the write path

Two load-bearing design points:

- **The suppression wrap lives in the base**, around the whole reaction. The observer-loop guard therefore cannot be forgotten or skipped by an override. (Suppression only gates writes, and every tracked write inside a reaction must be suppressed anyway — so wrapping the whole reaction is safe.)
- **Recompute from current truth, not from the event diff** — keeps replays idempotent, same principle as the cascade-marker retry design.

Abstracting `loadDocument` removed the base's `@Inject JsonStoreMongoService`, so `tracking` no longer depends on `service.jsonstore` — this broke the `tracking` ↔ `service.jsonstore` package cycle that was briefly accepted as debt. `DocMaterializeObserver` owns the jsonstore calls in its `loadDocument`/`recompute` overrides.

Accepted trade-off: the base is not behavior-agnostic — every subclass IS a reload-recompute-restamp reactor. A future observer with a different shape (fan-out cascade, pure notification) would need the template reopened.

Superseded along the way: an intermediate `applyRecomputedFields(event, fields)` persist hook was merged back into `recompute`.

## Related

- [[CDI observer methods are inherited from superclasses but not across packages if package-private]]
- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]
- [[luz_docs materialize passive retry via cascade markers]]
