---
ai_hash: 666d6584f3ede30f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: session 2026-06-30
status: seedling
tags:
- ollama
- local-llm
- coding
- models
title: Best Ollama models for CPU-only coding and research on a thin laptop
type: howto
---

# Best Ollama models for CPU-only coding and research on a thin laptop

For a CPU-only laptop (e.g. i7-1365U / 32 GB, no usable GPU — see [[Ollama does not accelerate on Intel Iris Xe iGPU (runs CPU-only)]]), pick models by **tokens/sec**, not memory. 7B is the comfortable ceiling; 14B+ runs but is too slow (~1-2 tok/s) to be pleasant.

Recommended set:
- **Coding (daily):** `qwen2.5-coder:7b` — best usable local code quality, ~3-7 tok/s, ~5 GB.
- **Fast autocomplete (FIM):** `qwen2.5-coder:3b` — snappy (~10-15 tok/s) for IDE completion (Continue.dev etc.).
- **Research / reasoning:** `qwen3:8b` — has a thinking mode strong for analysis & summarizing; drop to `qwen3:4b` for speed.
- **One model for both coding + research:** `qwen3:8b` is the best single-model compromise.

Tuning: keep Ollama's default **Q4_K_M** quant (best speed/quality on CPU; avoid Q8/fp16), set `num_ctx` ~4096-8192 (big contexts slow CPU inference a lot), stay plugged in (inference pegs cores, drains battery).

Reality check: local CPU models are good for offline/private bounded tasks (summarize pasted text, explain, draft). For research needing current web info or large docs, a hosted model (Claude with web search) beats anything local on this class of chip.

## Related

- [[Ollama does not accelerate on Intel Iris Xe iGPU (runs CPU-only)]]

%% ai-graph-start %%

**Related notes:**
- [[Ollama does not accelerate on Intel Iris Xe iGPU (runs CPU-only)]]
- [[Continue plugin shares one ~.continueconfig.yaml across all JetBrains IDEs and VS Code]]
- [[Disable qwen3 thinking mode via Ollama API thinkfalse, not the no_think prompt token]]

%% ai-graph-end %%