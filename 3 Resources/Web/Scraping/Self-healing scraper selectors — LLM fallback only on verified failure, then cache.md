---
ai_hash: 53c0b440b8ba8acb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: fb-info-project design discussion 2026-06-11
status: seedling
tags:
- scraping
- llm
- ollama
- architecture
- resilience
title: Self-healing scraper selectors — LLM fallback only on verified failure, then
  cache
type: model
---

# Self-healing scraper selectors — LLM fallback only on verified failure, then cache

Pattern for making a DOM scraper resilient to UI changes without paying LLM latency on every run — three tiers:

1. **Fast path (always):** deterministic selectors/regexes. Milliseconds, no model.
2. **Heal path (only when the fast path's *verified* action fails):** snapshot the candidate elements (e.g. all `role=button`/`role=menuitem` texts, trimmed), ask a small local LLM (Ollama, 3–8B, `format=json`) to pick the index that matches the *goal* ("which menu item shows ALL comments?"). Click it, then re-run the same verification the fast path uses — the verifier, not the model, is the source of truth.
3. **Cache:** persist the label/selector the LLM picked (small JSON next to the config). Steady state is zero LLM calls; the model is consulted once per site change, not per run/page/comment.

Anti-patterns: calling the LLM per item (hundreds of comments × seconds = unusable), trusting the LLM click without re-verification, and vision-model screenshot clicking when a text candidate list suffices (10–50× slower).

Works because the hard part of selector breakage is *semantic relabeling* (wording/language changes), which is exactly what an LLM classifies well from short text, while position/timing logic stays deterministic.

## Related

- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-start %%

**Related notes:**
- [[LLM-picked UI actions can be verified mechanically but not semantically]]
- [[Verify Facebook comment sort switch by re-reading the sort button label]]
- [[Build test fakes from verbatim production data, decoys included]]
- [[fb-info-project duplicates FB-fragile selectors across get_locations.py and scrapling_test.py]]
- [[Facebook's Newest sort option mentions 'all comments' in its description — anchor the label regex]]

%% ai-graph-end %%