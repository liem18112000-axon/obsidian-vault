---
title: "Adopt Google ADK only when the LLM drives the tool loop; else stay a2a-sdk-direct"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03"
tags: [adk, a2a, agentic, test-agent, architecture, decision]
---

# Adopt Google ADK only when the LLM drives the tool loop; else stay a2a-sdk-direct

The choice between Google ADK (adk-python) and building an agent directly on **a2a-sdk** is a question of the agent's **SHAPE, not the vendor**. Adopt ADK only for an agent where the **LLM drives the tool loop** (reason -> pick tool -> observe -> repeat). For a **deterministic, Python-driven pipeline** where the LLM is just one call inside a fixed flow (like the test-agent's KGA/TPD), stay on a2a-sdk directly.

**Why the framework rarely pays off for a pipeline agent**
- ADK is **built on the same foundation** you already use: a2a-sdk (A2A protocol) + MCP + Vertex. It **wraps** that base, it does not replace it — so a wholesale switch buys little.
- ADK is **Gemini-first**. Non-Gemini models (Claude) run only through a **LiteLlm wrapper** — an extra abstraction hop that hides the direct Vertex control you get from `anthropic[vertex]` (max_tokens, non-blocking calls, fallback).
- It **removes none of the real work**: the Atlassian crawl, ADF/HTML parsing, and gherkin rendering are all still yours to write.

**The one thing ADK genuinely adds**: an LLM-driven agent loop — `LlmAgent`, the `Sequential/Loop/Parallel` workflow agents, and Runner/Sessions/Memory services.

**Selective-adoption path** (the right way in, if/when needed): build just the LLM-driven agent as an `LlmAgent`, expose it with `to_a2a()`, and it slots into the existing A2A/MCP mesh alongside the a2a-sdk-direct agents — no rewrite of the others. In the test-agent, the natural landing spot is the client-side, LLM-driven **interrogation / refine loop**, not the crawl or plan pipelines.

Diagram: `test-agent/docs/adk-vs-current-stack.excalidraw`.

## Related
- [[test-agent common shared engine]]
- [[Interrogation loop asks nothing]]

## Related

- [[test-agent common shared engine]]
- [[Interrogation loop asks nothing]]
