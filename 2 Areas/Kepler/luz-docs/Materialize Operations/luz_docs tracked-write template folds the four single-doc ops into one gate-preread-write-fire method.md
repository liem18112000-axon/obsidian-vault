---
ai_hash: ae468dff8ed734af
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: TrackingJsonStoreClient template extraction, session 2026-06-05
status: budding
tags:
- luz-docs
- change-tracking
- template-method
- refactoring
title: luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire
  method
type: model
---

# luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire method

luz_docs `TrackingJsonStoreClient` folds its four tracked single-doc writes (insert/replace/update/delete) into one template method instead of four copies of the same flow:

```java
private Response tracked(String collection, Set<String> touchedFields, Supplier<JsonObject> beforeReader,
                         Supplier<Response> write, BiConsumer<JsonObject, Response> onSuccess)
```

Flow: gate (untracked collection / suppression / untouched fields) -> optional pre-read -> write -> fire-and-forget `onSuccess(before, response)` inside the never-throw `track()` wrapper. Null is the skip signal: `touchedFields == null` means "whole document may change, collection membership alone gates" (replace, delete); `beforeReader == null` means "no before state" (insert). The write `Supplier` is the single source for both the bypass and tracked branches — previously every method spelled the raw client call twice, which is the bug surface this removes (branches drifting apart). Plain JDK `Supplier`/`BiConsumer`, no custom functional interface.

## Related

- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[3 Resources/Languages/Java/CDI/Intercept an MP REST client by implementing its interface - unqualified inject resolves the wrapper, RestClient qualifier is the bypass]]

%% ai-graph-start %%

**Related notes:**
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
- [[luz_docs DocumentChangeObserver base owns the reload-recompute-restamp template]]
- [[luz_docs change tracking covers updateMany-deleteMany via projected before-after snapshots keyed by id]]
- [[Intercept an MP REST client by implementing its interface - unqualified inject resolves the wrapper, RestClient qualifier is the bypass]]
- [[Diff-based write tracking dies silently if the write runs before the pre-read]]

%% ai-graph-end %%