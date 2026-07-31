---
ai_hash: 66d0185b60c6f53e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: session 2026-06-30
status: seedling
tags:
- ollama
- local-llm
- gotcha
- hardware
title: Ollama does not accelerate on Intel Iris Xe iGPU (runs CPU-only)
type: lesson
---

# Ollama does not accelerate on Intel Iris Xe iGPU (runs CPU-only)

Ollama only offloads to GPUs via CUDA (NVIDIA), ROCm (AMD), or Metal (Apple). An Intel **Iris Xe integrated GPU** is none of these, so Ollama ignores it and runs **entirely on the CPU**. The practical consequence: on a thin-and-light Intel laptop, local LLM speed is bound by CPU cores, not by how much RAM you have.

**Gotcha:** Windows (`Win32_VideoController.AdapterRAM`) misreports the Iris Xe as ~2 GB of VRAM. It has no dedicated VRAM — it's an iGPU sharing system RAM. Don't size models against that fake number; size them against CPU throughput.

Concrete machine where this was confirmed: Intel Core i7-1365U (10-core U-series, only 2 P-cores), Iris Xe, 32 GB RAM, Windows 11 — gets ~3-7 tok/s on a 7B model, ~1-2 tok/s on 14B+.

## Related

- [[Best Ollama models for CPU-only coding and research on a thin laptop]]

%% ai-graph-start %%

**Related notes:**
- [[Best Ollama models for CPU-only coding and research on a thin laptop]]

%% ai-graph-end %%