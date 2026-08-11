---
ai_hash: 4dbbacc8a10a197f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-09
entities: []
source: session 2026-06-09 luz-docs MaterializeRepository fix
status: seedling
tags:
- java
- gotcha
- null-handling
- deserialization
title: empty-object-not-null sentinel defeats Optional.ofNullable null-guards
type: lesson
---

# empty-object-not-null sentinel defeats Optional.ofNullable null-guards

A deserializer that returns an **empty object `{}`** (rather than `null`) for an empty or missing payload silently defeats every null-based guard wrapped around it. `Optional.ofNullable(x)` is always *present*, `if (x == null)` never fires, and any `.orElseThrow(...)` / fallback branch becomes **dead code** — the empty object flows downstream and triggers the wrong failure mode (a permission/validation error, or a bogus empty 200) instead of a clean "not found".

The fix is to guard on a **field that only a real object has** — e.g. its identity key — not on the reference itself: `doc.get(ID) == null` rather than `doc == null`.

When wiring null-checks around any parse/deserialize call, first confirm what it actually returns for the empty case. "Returns null on empty" is an assumption that must be verified, not assumed.

See [[luz-docs getDocumentById returns empty object not null for missing docs]] for a concrete instance.

## Related

- [[luz-docs getDocumentById returns empty object not null for missing docs]]

%% ai-graph-start %%

**Related notes:**
- [[luz-docs getDocumentById returns empty object not null for missing docs]]
- [[JsonValue.NULL is a non-null Java object so ObjectsnonNull does not drop JSON null elements]]
- [[javax.json single-arg getString throws NPE on missing key]]
- [[luz-jsonstore find returns 200 empty string, not [], on zero matches]]
- [[JsonUtil.buildJsonObjectWithConditions for optional Mongo query fields (luz-docs)]]

%% ai-graph-end %%