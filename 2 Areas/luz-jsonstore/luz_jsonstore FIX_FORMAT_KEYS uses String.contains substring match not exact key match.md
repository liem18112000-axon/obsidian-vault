---
title: "luz_jsonstore FIX_FORMAT_KEYS uses String.contains substring match not exact key match"
created: 2026-08-17
type: lesson
status: seedling
source: "session 2026-08-17"
tags: [luz-jsonstore, gotcha, config]
---

# luz_jsonstore FIX_FORMAT_KEYS uses String.contains substring match not exact key match

The field-selection guard in \`tempAdjustFormat\` is \`Constants.CH_KLARA_JSONSTORE_FIX_FORMAT_KEYS.contains(key)\` — and \`CH_KLARA_JSONSTORE_FIX_FORMAT_KEYS\` is a plain \`String\`, so this is a \`String.contains\` **substring** test, not set/exact membership.

**Consequence:** any field name that happens to be a substring of the configured keys string matches. E.g. with keys configured as \`"accountNumber"\`, a field literally named \`"count"\` or \`"Number"\` would match and get coerced. This is a latent false-positive source when picking which fields to int-coerce.

**Extra trap:** the constant is built as \`"" + System.getenv("CH_KLARA_JSONSTORE_FIX_FORMAT_KEYS")\`, so when the env var is unset the string is the literal \`"null"\` — meaning fields named \`n\`, \`u\`, \`l\`, \`nu\`, \`null\`, etc. would technically match (the master \`CH_KLARA_JSONSTORE_FIX_FORMAT\` switch normally gates this, but the two flags are independent).

Part of [[luz_jsonstore tempAdjustFormat coerces Double fields to Integer on read]].

## Related

- [[luz_jsonstore tempAdjustFormat coerces Double fields to Integer on read]]
