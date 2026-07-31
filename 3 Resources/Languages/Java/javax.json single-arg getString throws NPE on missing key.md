---
title: "javax.json single-arg getString throws NPE on missing key"
created: 2026-06-09
type: gotcha
status: seedling
tags: [java, javax-json, json-p, jakarta, gotcha, npe, luz-docs]
entities: [JsonObject.getString, getInt, getBoolean, getJsonObject, getJsonArray, Optional.ofNullable]
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
