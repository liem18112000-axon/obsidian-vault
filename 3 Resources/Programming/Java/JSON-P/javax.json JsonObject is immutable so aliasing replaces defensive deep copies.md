---
title: "javax.json JsonObject is immutable so aliasing replaces defensive deep copies"
created: 2026-06-04
type: concept
status: seedling
source: "session 2026-06-04"
tags: [java, json-p, immutability, code-review]
---

# javax.json JsonObject is immutable so aliasing replaces defensive deep copies

`javax.json.JsonObject` / `JsonArray` (JSON-P, JSR-374) are **immutable** once built — every "update" util (e.g. luz_docs `JsonObjectUtil.updateFieldJsonArrayOfJsonObject`, `updateValue`) copies into a new `Json.createObjectBuilder(obj)` and returns `build()`. So holding a second reference (`JsonObject current = metadata;`) before rebinding the variable IS a safe snapshot — no deep copy needed, ever.

Why it matters: code-review instinct says "that alias needs a defensive copy", but that only applies to mutable types. Reassignment (`metadata = util(...)`) rebinds the local; the old object is untouched. A Mockito verify on the old reference (asserting it still has pre-update fields) is an easy way to prove it to a reviewer.

See [[Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]].

## Related

- [[Unit-testing FolderService recoverFolder requires per-collection stubs because process objects call back into the real service]]
