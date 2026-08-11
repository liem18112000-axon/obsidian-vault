---
ai_hash: 392dc4086e2dfb95
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: Deep research 2026-07-22 — gorse.io docs + gorse-io/gorse v0.5.11 source
status: budding
tags:
- gorse
- config
- machine-learning
title: Gorse config exposes model family and cadence, never hyperparameters
type: concept
---

# Gorse config exposes model family and cadence, never hyperparameters

Gorse's config surface deliberately exposes only the model *family* and the training *cadence* — never individual hyperparameters.

- Collaborative filtering: `[recommend.collaborative] type = "none" | "mf"` — matrix factorization only, trained by **BPR** (pairwise) or **eALS** (point-wise, registered internally as `als`).
- Ranking: `[recommend.ranker] type = "none" | "fm" | "llm"` (FM = factorization machines), plus a `recommenders = [...]` list that decides which offline sources are merged into the final recommendation.
- Cadence knobs only: `fit_period`, `fit_epoch`, `optimize_period`, `optimize_trials`, `early_stopping.patience`.

Latent factors, learning rate, regularization, and the BPR-vs-eALS choice are all picked by Gorse's automatic model search — no config keys exist for them. A different model family means forking: the model registries are closed enums (`model/cf` accepts only `bpr`/`als`; ranker validated `oneof=none fm llm`), and there is no plugin API.

## Related

- [[Gorse gotcha - CF and hyperparameter search are disabled by code defaults]]
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]

%% ai-graph-start %%

**Related notes:**
- [[Gorse gotcha - CF and hyperparameter search are disabled by code defaults]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse LLM reranker is a rerank-API integration, not chat completions]]

%% ai-graph-end %%