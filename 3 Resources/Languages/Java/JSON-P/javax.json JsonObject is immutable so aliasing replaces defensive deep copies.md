---
ai_hash: 77ea9d4c04bc5a00
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities: []
source: session 2026-06-04
status: seedling
tags:
- java
- json-p
- immutability
- code-review
title: javax.json JsonObject is immutable so aliasing replaces defensive deep copies
type: concept
---

# javax.json JsonObject is immutable so aliasing replaces defensive deep copies

`javax.json.JsonObject` / `JsonArray` (JSON-P, JSR-374) are **immutable** once built — every "update" util (e.g. luz_docs `JsonObjectUtil.updateFieldJsonArrayOfJsonObject`, `updateValue`) copies into a new `Json.createObjectBuilder(obj)` and returns `build()`. So holding a second reference (`JsonObject current = metadata;`) before rebinding the variable IS a safe snapshot — no deep copy needed, ever.

Why it matters: code-review instinct says "that alias needs a defensive copy", but that only applies to mutable types. Reassignment (`metadata = util(...)`) rebinds the local; the old object is untouched. A Mockito verify on the old reference (asserting it still has pre-update fields) is an easy way to prove it to a reviewer.

See [[Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]].

## Related

- [[Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]]

%% ai-graph-start %%

**Related notes:**
- [[JSON-P createArrayBuilder(Collection) rejects built JsonValues]]
- [[Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]]
- [[Interaction-style mocks hide ordering bugs that a stateful in-memory fake exposes]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]

%% ai-graph-end %%