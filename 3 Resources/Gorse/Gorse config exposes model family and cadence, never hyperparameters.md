---
title: "Gorse config exposes model family and cadence, never hyperparameters"
created: 2026-07-22
type: concept
status: budding
source: "Deep research 2026-07-22 — gorse.io docs + gorse-io/gorse v0.5.11 source"
tags: [gorse, config, machine-learning]
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
