---
ai_hash: 5a18ae3f6816a09a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities:
- tracking.DocumentChangeObserver
- DocMaterializeObserver
- reload-recompute-restamp template
- tracked document writes
- '@ObservesAsync observer method'
- shouldSkip(event)
- loadDocument(event)
- recompute(event, document)
- ChangeTrackingSuppression.suppress()
- onDocumentChangeFailed(event, e)
- JsonStoreMongoService
- tracking
- service.jsonstore
- CDI observer methods
- ChangeOrigin event marker
- thread-local suppression
- cascade-marker retry design
- applyRecomputedFields(event, fields)
- current truth
- event diff
- jsonstore calls
- luz_docs change tracking
- luz_docs materialize
- passive retry
- superclasses
source: DocumentChangeObserver refactor, session 2026-06-05
status: budding
tags:
- luz-docs
- change-tracking
- cdi-events
- template-method
- design-decision
title: luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template
type: model
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

%% ai-graph-start %%

**Related notes:**
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
- [[luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire method]]
- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[CDI observer methods are inherited from superclasses but not across packages if package-private]]
- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]

**Relations:**
- tracking.DocumentChangeObserver — *is an abstract base* — CDI
- tracking.DocumentChangeObserver — *owns* — reload-recompute-restamp template
- DocMaterializeObserver — *is a concrete observer* — tracking.DocumentChangeObserver
- DocMaterializeObserver — *fills in hooks of* — tracking.DocumentChangeObserver
- reload-recompute-restamp template — *handles* — tracked document writes
- reload-recompute-restamp template — *includes* — @ObservesAsync observer method
- reload-recompute-restamp template — *includes* — shouldSkip(event)
- reload-recompute-restamp template — *includes* — loadDocument(event)
- reload-recompute-restamp template — *includes* — recompute(event, document)
- recompute(event, document) — *is executed inside* — ChangeTrackingSuppression.suppress()
- reload-recompute-restamp template — *handles failures via* — onDocumentChangeFailed(event, e)
- ChangeTrackingSuppression.suppress() — *lives in* — tracking.DocumentChangeObserver
- ChangeTrackingSuppression.suppress() — *wraps* — whole reaction
- recompute(event, document) — *uses source* — current truth
- recompute(event, document) — *avoids source* — event diff
- recompute(event, document) — *shares principle with* — cascade-marker retry design
- loadDocument(event) — *removed dependency on* — JsonStoreMongoService
- tracking — *no longer depends on* — service.jsonstore
- tracking — *had package cycle with* — service.jsonstore
- DocMaterializeObserver — *handles* — jsonstore calls
- jsonstore calls — *are in DocMaterializeObserver's overrides of* — loadDocument(event)
- jsonstore calls — *are in DocMaterializeObserver's overrides of* — recompute(event, document)
- tracking.DocumentChangeObserver — *is not* — behavior-agnostic
- tracking.DocumentChangeObserver — *subclasses are* — reload-recompute-restamp reactor
- applyRecomputedFields(event, fields) — *was merged into* — recompute(event, document)
- CDI observer methods — *are inherited from* — superclasses
- luz_docs change tracking — *dropped* — ChangeOrigin event marker
- thread-local suppression — *is the* — loop guard
- luz_docs materialize — *uses* — cascade markers
- cascade markers — *are for* — passive retry

%% ai-graph-end %%