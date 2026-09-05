---
title: "ADK serverless agent tier is CPU-only; LLM compute is offloaded to a managed model API"
created: 2026-09-03
type: lesson
status: seedling
source: "research session 2026-09-03"
tags: [adk, gcp, agent-engine, cloud-run, serverless, compute]
---

# ADK serverless agent tier is CPU-only; LLM compute is offloaded to a managed model API

When you deploy a Google ADK (Agent Development Kit) agent to a **serverless** target, the agent process is a lightweight **CPU-only orchestration workload** — it runs the Python reasoning loop, tool calls, and session state. The heavy "AI compute" (the LLM) is **not on that machine**: it is a remote call to a managed model API (Gemini / Vertex AI) that runs on Google's own GPU/TPU fleet you never provision.

**Consequence:** for a normal "agent calls Gemini" pattern you want **CPU boxes and no GPU/TPU**. GPU/TPU on the agent tier only matters if you *co-locate a model* with the agent (self-hosted OSS LLM, embedding/reranker, Whisper, etc.). This is why Vertex AI Agent Engine (the purpose-built ADK runtime) exposes **only CPU + memory knobs** — the absence of a GPU setting is the design tell.

Applies to the `test-agent` project too: it calls Vertex/Gemini remotely, so Cloud Run CPU instances are correct and the GPU/TPU question is moot.

## Related

- [[ADK deploy targets compute matrix: Agent Engine vs Cloud Run vs GKE (CPU/GPU/TPU)]]
