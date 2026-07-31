---
title: "Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template"
created: 2026-07-22
type: lesson
status: budding
source: "Deep research 2026-07-22 — 5 v0.4-era claims refuted 0-3 in adversarial verification"
tags: [gorse, gotcha, docs-drift, versioning]
---

# Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template

Gorse v0.5 restructured `config.toml`, but v0.4 docs still rank highly in web search and *actively mislead*: in a verified deep-research pass, five plausible-sounding v0.4-era claims were refuted 0-3.

Gone in v0.5: `[recommend.offline]`, `[recommend.online]`, `[recommend.popular]` + `popular_window`, `[openai]`, `model_fit_period` / `model_search_period` / `model_search_trials`, `check_recommend_period` / `refresh_recommend_period`.
Replacements: Expr-defined `[[recommend.non-personalized]]` leaderboards (for popular), `[recommend.fallback]` (default `["latest"]`), `[recommend.replacement]` (`enable_replacement`, decays 0.8 / 0.6), and per-block `fit_period` / `optimize_period` keys.

Two broader lessons: (1) docs-vs-code drift exists even on *current* pages — when they disagree, the shipped `config.toml` template in the repo is ground truth; (2) Gorse is pre-1.0 and minor versions are breaking (config + DB schema), so pin versions and re-verify config on upgrade.

## Related

- [[Gorse gotcha - CF and hyperparameter search are disabled by code defaults]]
