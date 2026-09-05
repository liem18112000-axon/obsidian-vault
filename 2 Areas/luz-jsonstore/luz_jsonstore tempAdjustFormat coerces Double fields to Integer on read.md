---
title: "luz_jsonstore tempAdjustFormat coerces Double fields to Integer on read"
created: 2026-08-17
type: howto
status: seedling
source: "session 2026-08-17"
tags: [luz-jsonstore, mongodb, bson, data-normalization]
---

# luz_jsonstore tempAdjustFormat coerces Double fields to Integer on read

In \`JsonStoreMongoDbService\`, \`tempAdjustFormat(Document)\` is a recursive, in-place normalization pass applied to results just before they are returned to callers — it runs in \`getOne\`, \`getMany\`, \`aggregate\`, and \`findOneAndUpdate\`. Its only real job is to coerce values that deserialized as \`Double\` back into \`Integer\` for a configured set of field names (a band-aid for JSON having no int type, so `5` came back as `5.0`).

**Env-gated no-op by default.** The entire body is skipped unless \`Constants.CH_KLARA_JSONSTORE_FIX_FORMAT\` is true, which is true only when the env var \`CH_KLARA_JSONSTORE_FIX_FORMAT\` is set to anything (even empty). In any env without that var, the method does nothing.

**Truncates, does not round.** The helper \`convert()\` does \`((Double)value).intValue()\`, which truncates toward zero rather than rounding: \`5.0->5\`, \`5.9->5\`, \`-1.7->-1\`.

Marked \`// TEMP\` in \`Constants.java\` — intended as a stopgap. See related gotchas: [[luz_jsonstore FIX_FORMAT_KEYS uses String.contains substring match not exact key match]] and [[luz_jsonstore tempAdjustFormat overwrites whole list when targeted key holds a numeric array]].

## Related

- [[luz_jsonstore FIX_FORMAT_KEYS uses String.contains substring match not exact key match]]
- [[luz_jsonstore tempAdjustFormat overwrites whole list when targeted key holds a numeric array]]
