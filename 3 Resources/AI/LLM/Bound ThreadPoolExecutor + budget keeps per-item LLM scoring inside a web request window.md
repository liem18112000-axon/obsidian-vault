---
ai_hash: ec0ff6271eea3cd5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-14
entities: []
source: Accesstrade integration, session 2026-06-13
status: seedling
tags:
- concurrency
- threadpool
- llm
- python
- performance
- web
title: Bound ThreadPoolExecutor + budget keeps per-item LLM scoring inside a web request
  window
type: lesson
---

# Bound ThreadPoolExecutor + budget keeps per-item LLM scoring inside a web request window

When a **synchronous** web endpoint scores N items and each item costs one independent, slow (~10s+) LLM network call, a serial loop blows past the request timeout (e.g. 16 items x 13s ≈ 3-4 min). Two combined moves fix it **without** changing the synchronous UX:

1. **Budget** — cap how many items actually get an LLM call, spending the budget on the most promising items first (e.g. highest reward); the rest are scored by deterministic rules only. This bounds the call count regardless of account size.
2. **Bounded concurrency** — run the budgeted calls across a small `ThreadPoolExecutor` (e.g. 4 workers). The calls are I/O-bound (network), so threads give real speedup despite the GIL. 12 calls / 4 workers ≈ 3 batches instead of 12 serial.

**Keep the scorer single-shaped:** precompute the LLM-derived dimensions into a dict keyed by item id, then feed them into the per-item scorer via an optional override param (e.g. `llm_dims=...`). The scorer uses the override when present, else makes the call inline — so the same function works in both inline and precomputed/parallel modes, and tests stay unchanged.

**Thread-safety requirement:** the LLM client must be safe to call from multiple threads — either stateless HTTP (Ollama) or one that builds a fresh client per call (google-genai). Do all DB writes on the main thread; let worker threads do only the pure LLM call.

```python
with ThreadPoolExecutor(max_workers=workers) as pool:
    futures = {pool.submit(rate, llm, item, strategy): item.id for item in budgeted}
    for fut, item_id in futures.items():
        dims_by_id[item_id] = fut.result()
```

Context: `StrategyRecommender.recommend` in the Accesstrade toolkit (Vertex ~13s/call). Related: [[google-genai Client must be held in a variable during the request or it is GC-closed]].

## Related

- [[google-genai Client must be held in a variable during the request or it is GC-closed]]

%% ai-graph-start %%

**Related notes:**
- [[Acquire a client-side rate limiter once per call, outside the retry loop]]
- [[Set HTTPserverless maxDuration above the internal LLM-run timeout, not below]]
- [[Client-side min-interval rate limiting via slot reservation]]

%% ai-graph-end %%