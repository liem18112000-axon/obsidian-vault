---
ai_hash: 48c7269615e026c6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: session 2026-06-30
status: seedling
tags:
- ollama
- qwen3
- api
- gotcha
title: Disable qwen3 thinking mode via Ollama API think:false, not the /no_think prompt
  token
type: lesson
---

# Disable qwen3 thinking mode via Ollama API think:false, not the /no_think prompt token

To turn off qwen3's reasoning/thinking pass in Ollama, pass a top-level **`think: false`** field in the `/api/generate` (or chat) JSON body. This is cleaner than putting the `/no_think` token in the prompt: with `/no_think`, qwen3 still emits empty `<think></think>` tags first, and if you cap output with a small `num_predict`, the response can come back **blank** because the cap is exhausted before the real answer starts.

Example body: `{ "model": "qwen3:8b", "prompt": "...", "stream": false, "think": false, "options": { "num_predict": 120 } }`

Leave `think` unset (or true) for research/analysis where the reasoning pass helps. See [[Best Ollama models for CPU-only coding and research on a thin laptop]].

## Related

- [[Best Ollama models for CPU-only coding and research on a thin laptop]]

%% ai-graph-start %%

**Related notes:**
- [[Best Ollama models for CPU-only coding and research on a thin laptop]]

%% ai-graph-end %%