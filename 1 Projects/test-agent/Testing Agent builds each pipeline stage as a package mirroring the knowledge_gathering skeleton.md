---
title: "Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton"
created: 2026-08-28
type: model
status: seedling
source: "session 2026-08-28, test-plan-definition implementation plan"
tags: [test-agent, a2a, mcp, agent-architecture, design-decision]
---

# Testing Agent builds each pipeline stage as a package mirroring the knowledge_gathering skeleton

The Testing Agent pipeline (knowledge_gathering -> test_plan_definition -> execution -> ...) is built so that **each stage is its own package that mirrors the `knowledge_gathering` skeleton**, rather than one monolith. The shared skeleton is: an **A2A agent** (`server.py` + `executor/` router) fronted by an **A2A->MCP bridge** so local Claude drives it, a **shared GCS Memory Bank** (JSON sidecar canonical + rendered Markdown), a **Claude-on-Vertex vs heuristic split** (now centralized in `knowledge_gathering.llm` via `vertex_config()`), and a **resumable, multi-turn interrogation loop** that pauses in A2A `input-required`.

**Key insight — same building blocks, reordered.** `knowledge_gathering` does *generate -> reconfirm*: `gather` (one-shot crawl) then `refine` (multi-turn reconfirm, rounds business/technical/qa). The next package `test_plan_definition` does *reconfirm -> generate*: `define` (multi-turn reconfirm, rounds **methodology/scope/metrics** -> a confirmed `TestPlan`) then `implement` (one-shot generation of test data + happy/negative scenarios + steps). Same two primitives (reconfirm loop + one-shot generator), flipped order.

**Reuse, do not duplicate.** The new package depends on `knowledge_gathering` for the generic contracts Question/Answer/Insight/Graph/Pack/MemoryBank and `llm.vertex.vertex_config`. `define` is essentially `RefineSession` with different round names + prompts. The **hand-off between stages is the approved insight pack, keyed by `context_id`** (produced by the `approve` step), read from the shared bank.

**Why:** one skeleton to learn/debug, warm-start persistence in the shared bank, and provenance edges linking scenario -> insight -> source note across stages.

See also [[JetBrains Excalidraw plugin rewrites the .excalidraw source field on save]].

## Related

- [[JetBrains Excalidraw plugin rewrites the .excalidraw source field on save]]
