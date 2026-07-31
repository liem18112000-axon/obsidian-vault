---
ai_hash: d1b25c5210c81958
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities: []
source: Deep research 2026-07-22 — gorse.io/posts/llm-ranker + config.go RerankerAPIConfig
  (v0.5.8+)
status: budding
tags:
- gorse
- llm
- reranking
title: Gorse LLM reranker is a rerank-API integration, not chat completions
type: concept
---

# Gorse LLM reranker is a rerank-API integration, not chat completions

Gorse's LLM ranking option (v0.5.8+), enabled with `[recommend.ranker] type = "llm"`, integrates a Jina-style **rerank API** (example model `qwen3-rerank`) — it is *not* a chat-completions endpoint. The endpoint is configured under `[recommend.ranker.reranker_api]` (`url`, `model`, `auth_token`); Jinja2 templates shape the request — `query_template` renders the user's recent feedback (count controlled by `recommend.context_size`) and `document_template` renders each candidate item.

The v0.4-era `[openai]` chat integration is gone from current config; a chat-based path survives only as the undocumented item-to-item `type = "chat"` on master.

## Related

- [[3 Resources/Data/Gorse/Gorse config exposes model family and cadence, never hyperparameters]]

%% ai-graph-start %%

**Related notes:**
- [[Gorse precomputes recommendations offline — per-request scoring is impossible inside it]]
- [[Gorse v0.4 docs describe a defunct config schema — trust the shipped config.toml template]]
- [[LEO Personalization Engine uses config-first Gorse plus a Python re-rank layer]]
- [[Gorse config exposes model family and cadence, never hyperparameters]]
- [[Gorse v0.5 declares custom recommenders as named config blocks with Expr expressions]]

%% ai-graph-end %%