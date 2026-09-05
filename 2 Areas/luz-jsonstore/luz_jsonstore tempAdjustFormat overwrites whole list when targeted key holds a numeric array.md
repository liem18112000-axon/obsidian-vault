---
title: "luz_jsonstore tempAdjustFormat overwrites whole list when targeted key holds a numeric array"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [luz-jsonstore, bug, gotcha]
---

# luz_jsonstore tempAdjustFormat overwrites whole list when targeted key holds a numeric array

In \`tempAdjustFormat\`, when a value is a \`List\` the code iterates its elements, and for a scalar element that is a targeted \`Double\` it executes \`document.put(key, convert(key, item))\`. That writes the converted scalar back under the **list key**, so it replaces the ENTIRE list with a single \`Integer\` (effectively the last converted element) instead of converting each element in place.

**Impact:** latent bug. It is currently harmless only because the targeted keys hold scalars, not numeric arrays. If any key in \`CH_KLARA_JSONSTORE_FIX_FORMAT_KEYS\` ever points at a \`List<Double>\`, that field would be silently corrupted from an array into a lone integer on read.

**Correct fix would be** to mutate the list element by index (e.g. \`list.set(i, convert(...))\`) rather than \`document.put(key, ...)\`.

Part of [[luz_jsonstore tempAdjustFormat coerces Double fields to Integer on read]].

## Related

- [[luz_jsonstore tempAdjustFormat coerces Double fields to Integer on read]]
