---
ai_hash: 0cc4a0c1e96a1deb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
status: seedling
tags:
- java
- javax-json
- json-p
- jakarta
- gotcha
- npe
- luz-docs
title: javax.json single-arg getString throws NPE on missing key
type: gotcha
---

# javax.json single-arg getString throws NPE on missing key

Jakarta JSON-P's single-arg `JsonObject.getString(String name)` **throws `NullPointerException` when the key is absent** — it does not return null. The impl is roughly `((JsonString) get(name)).getString()`, so a missing key NPEs on the cast of `null`.

**Fix: use the 2-arg `getString(name, defaultValue)`** for any missing-safe read.

```java
// BAD — NPE if the field is absent; Optional never sees a value
Optional.ofNullable(metadata.getString(Constants.ORIGIN)).map(...)
// GOOD
Optional.ofNullable(metadata.getString(Constants.ORIGIN, null)).map(...)
```

**Why it bites:** wrapping the single-arg form in `Optional.ofNullable(...)` is **dead code** — the method throws *instead of* returning null, so the guard can never fire. Same trap on every typed single-arg accessor: `getInt`, `getBoolean`, `getJsonObject`, `getJsonArray`.

**Failure signatures seen in luz-docs:**
- `AnalyzeService.performDiscoverEnrichers` — NPE rethrown as `EnricherException` at `DocumentDiscoverEnricher:90` with an empty `Caused by: NullPointerException`. That empty-cause shape is the classic tell of a missing-key getString deep in a call chain.
- A defensive `catch` block called single-arg `getString("_id")` on a doc that might lack `_id` → a SECOND NPE *inside the catch*, which escaped a `CompletableFuture.runAsync` lambda, completed the future exceptionally, and aborted the whole batch at `allOf(...).join()`. One bad record killed the campaign.

## Related

- [[Java]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs getDocumentById returns empty object not null for missing docs]]
- [[empty-object-not-null sentinel defeats Optional.ofNullable null-guards]]
- [[JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]
- [[luz-jsonstore find returns 200 empty string, not [], on zero matches]]
- [[JsonParsingException EOF offset -1 means an empty response body was parsed]]

%% ai-graph-end %%