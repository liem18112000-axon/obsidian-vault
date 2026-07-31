---
ai_hash: dccff9b15960e50b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: Deep research 2026-07-22 — gorse.io v0.5 release post + common/expression/expression.go
status: budding
tags:
- gorse
- feedback
- config
title: Gorse feedback value expressions classify events conditionally, not weight
  them
type: concept
---

# Gorse feedback value expressions classify events conditionally, not weight them

Since v0.5, Gorse feedback rows carry a `Value` field and `positive_feedback_types` in `[recommend.data_source]` accepts threshold expressions over it: `"read>=3"` means a read event counts as positive only when its value is at least 3 (operators `<`, `<=`, `>`, `>=`, regex-parsed in `common/expression/expression.go`).

This is **conditional classification** — deciding whether an event is positive at all — not per-event training weights: you cannot express "a purchase weighs 5x a click". Practical consequence: always send a meaningful `Value` with feedback (counts, durations) so threshold tuning stays available later without re-ingesting.

## Related

- [[3 Resources/Data/Gorse/Gorse config exposes model family and cadence, never hyperparameters]]

%% ai-graph-start %%

**Related notes:**
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse config exposes model family and cadence, never hyperparameters]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]
- [[Gorse gotcha - CF and hyperparameter search are disabled by code defaults]]

%% ai-graph-end %%