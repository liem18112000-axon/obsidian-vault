---
title: "javax.json single-arg getString throws NPE on missing key"
created: 2026-06-09
type: lesson
status: seedling
source: "luz-docs materialize code review 2026-06-09"
tags: [java, json-p, jakarta, gotcha, npe]
---

# javax.json single-arg getString throws NPE on missing key

Jakarta JSON-P's single-arg `JsonObject.getString(String name)` **throws `NullPointerException` when the key is absent** — it does not return null. The internal impl is roughly `((JsonString) get(name)).getString()`, so a missing key NPEs on the cast of `null`.

**Use the 2-arg `getString(name, defaultValue)`** for any missing-safe read; it returns the default instead of throwing.

**Why it bites:** wrapping the single-arg form in `Optional.ofNullable(obj.getString(name))` is **dead code** — the method throws *instead of* returning null, so the Optional can never guard the missing-key case.

Real bug: an error-handling `catch` block called single-arg `getString("_id")` on a document that might lack `_id`. That threw a SECOND NPE *inside the catch*, which escaped a `CompletableFuture.runAsync` lambda, completed the future exceptionally, and aborted the whole batch at `allOf(...).join()`. A defensive catch turned one bad record into a total-campaign failure.

Same trap applies to the other typed single-arg accessors (`getInt`, `getBoolean`, `getJsonArray`, `getJsonObject`) — prefer the defaulted overloads on untrusted/optional input.

## Related

- [[Java]]
