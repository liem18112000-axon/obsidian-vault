---
title: "Gorse feedback value expressions classify events conditionally, not weight them"
created: 2026-07-22
type: concept
status: budding
source: "Deep research 2026-07-22 — gorse.io v0.5 release post + common/expression/expression.go"
tags: [gorse, feedback, config]
---

# Gorse feedback value expressions classify events conditionally, not weight them

Since v0.5, Gorse feedback rows carry a `Value` field and `positive_feedback_types` in `[recommend.data_source]` accepts threshold expressions over it: `"read>=3"` means a read event counts as positive only when its value is at least 3 (operators `<`, `<=`, `>`, `>=`, regex-parsed in `common/expression/expression.go`).

This is **conditional classification** — deciding whether an event is positive at all — not per-event training weights: you cannot express "a purchase weighs 5x a click". Practical consequence: always send a meaningful `Value` with feedback (counts, durations) so threshold tuning stays available later without re-ingesting.

## Related

- [[3 Resources/Data/Gorse/Gorse config exposes model family and cadence, never hyperparameters]]
