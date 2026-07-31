---
title: "javax.json getString(name) throws NPE on missing key"
created: 2026-06-09
type: gotcha
status: seedling
source: "session 2026-06-09"
tags: [java, javax-json, gotcha, npe, luz-docs]
---

# javax.json getString(name) throws NPE on missing key

In `javax.json`, the single-arg `JsonObject.getString(String name)` **throws `NullPointerException` when the key is absent** — it does not return null. So `Optional.ofNullable(obj.getString(name))` is a trap: the NPE fires *inside* `getString` before `ofNullable` ever sees the value, so the Optional guard does nothing.

For optional keys, always use the two-arg overload `getString(name, defaultValue)`, which returns the default when the key is missing.

**Real bug (luz-docs, AnalyzeService.performDiscoverEnrichers ~line 149):**
```java
// BAD — NPE if document has no "origin" field
Optional.ofNullable(metadata.getString(Constants.ORIGIN)).map(...)
// GOOD
Optional.ofNullable(metadata.getString(Constants.ORIGIN, null)).map(...)
```
The NPE surfaced wrapped as `EnricherException` at `DocumentDiscoverEnricher:90` (the catch block rethrow), with an empty `Caused by: NullPointerException` cause — classic signature of a missing-key getString deep in the call chain. Same file had a safe sibling call inside a try/catch (swallowed) and another using the two-arg form, which is how the offending line stood out.

Applies to `getInt`, `getJsonObject`, etc. too — the single-arg getters throw on missing key; prefer the defaulted overloads for optional fields.
