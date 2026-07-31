---
title: "empty-object-not-null sentinel defeats Optional.ofNullable null-guards"
created: 2026-06-09
type: lesson
status: seedling
source: "session 2026-06-09 luz-docs MaterializeRepository fix"
tags: [java, gotcha, null-handling, deserialization]
---

# empty-object-not-null sentinel defeats Optional.ofNullable null-guards

A deserializer that returns an **empty object `{}`** (rather than `null`) for an empty or missing payload silently defeats every null-based guard wrapped around it. `Optional.ofNullable(x)` is always *present*, `if (x == null)` never fires, and any `.orElseThrow(...)` / fallback branch becomes **dead code** — the empty object flows downstream and triggers the wrong failure mode (a permission/validation error, or a bogus empty 200) instead of a clean "not found".

The fix is to guard on a **field that only a real object has** — e.g. its identity key — not on the reference itself: `doc.get(ID) == null` rather than `doc == null`.

When wiring null-checks around any parse/deserialize call, first confirm what it actually returns for the empty case. "Returns null on empty" is an assumption that must be verified, not assumed.

See [[luz-docs getDocumentById returns empty object not null for missing docs]] for a concrete instance.

## Related

- [[luz-docs getDocumentById returns empty object not null for missing docs]]
