---
ai_hash: 5354b94635fb2613
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: Deep research 2026-07-22 — 5 v0.4-era claims refuted 0-3 in adversarial verification
status: budding
tags:
- gorse
- gotcha
- docs-drift
- versioning
title: Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml
  template
type: lesson
---

# Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template

Gorse v0.5 restructured `config.toml`, but v0.4 docs still rank highly in web search and *actively mislead*: in a verified deep-research pass, five plausible-sounding v0.4-era claims were refuted 0-3.

Gone in v0.5: `[recommend.offline]`, `[recommend.online]`, `[recommend.popular]` + `popular_window`, `[openai]`, `model_fit_period` / `model_search_period` / `model_search_trials`, `check_recommend_period` / `refresh_recommend_period`.
Replacements: Expr-defined `[[recommend.non-personalized]]` leaderboards (for popular), `[recommend.fallback]` (default `["latest"]`), `[recommend.replacement]` (`enable_replacement`, decays 0.8 / 0.6), and per-block `fit_period` / `optimize_period` keys.

Two broader lessons: (1) docs-vs-code drift exists even on *current* pages — when they disagree, the shipped `config.toml` template in the repo is ground truth; (2) Gorse is pre-1.0 and minor versions are breaking (config + DB schema), so pin versions and re-verify config on upgrade.

## Related

- [[Gorse gotcha - CF and hyperparameter search are disabled by code defaults]]

%% ai-graph-start %%

**Related notes:**
- [[Gorse gotcha - CF and hyperparameter search are disabled by code defaults]]
- [[Gorse config exposes model family and cadence, never hyperparameters]]
- [[Gorse LLM reranker is a rerank-API integration, not chat completions]]
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]

%% ai-graph-end %%