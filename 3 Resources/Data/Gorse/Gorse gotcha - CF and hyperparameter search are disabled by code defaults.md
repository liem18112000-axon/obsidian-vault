---
title: "Gorse gotcha - CF and hyperparameter search are disabled by code defaults"
created: 2026-07-22
type: lesson
status: budding
source: "Deep research 2026-07-22 — gorse.io docs vs gorse-io/gorse v0.5.11 code"
tags: [gorse, gotcha, config]
---

# Gorse gotcha - CF and hyperparameter search are disabled by code defaults

Three Gorse v0.5 settings silently ship disabled (or different) in code even though the docs suggest otherwise:

1. **Collaborative filtering is OFF by default** — `[recommend.collaborative] type` defaults to `"none"`; personalized CF only runs once you set `type = "mf"`.
2. **Hyperparameter auto-search is OFF by default in code** — `optimize_period` defaults to `0` (search disabled) while the docs table claims `360m`.
3. **Ranker default mismatch** — code defaults `type = "none"`, docs say `"fm"`; the shipped `config.toml` template sets `"fm"`.

Lesson: set all three explicitly, and treat the shipped `config.toml` template as ground truth over docs tables.

## Related

- [[3 Resources/Data/Gorse/Gorse config exposes model family and cadence, never hyperparameters]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]
