---
title: "Multi-level altitude reporting from a single-source agent answer"
created: 2026-08-17
type: concept
status: seedling
source: "session 2026-08-17 agent-framework-skeleton diagram"
tags: [agents, reporting, design-pattern]
---

# Multi-level altitude reporting from a single-source agent answer

A reporting pattern for agent frameworks: the agent computes **one** single-source result ("Info / Data / Answer" — the source of truth), then **renders that same result at several audience altitudes** instead of producing separate analyses. The answer is fixed; only the framing and level of detail change.

Four altitudes used in practice (detail increases as you go down):
- **Level 1 · C-level** — outcome, risk, cost; ~3 bullets.
- **Level 2 · PO / Customer** — features, value delivered, status.
- **Level 3 · Developer** — APIs, changes, how-to, tickets.
- **Level 4 · Deep-dive** — root cause, internals, traces.

**Why it's useful:** one computation, many consumers; guarantees the exec summary and the deep-dive can't contradict each other because they derive from the same core. Pairs with an "Interrogation (Q/A)" mode over the same core for on-demand, conversational follow-ups.

Built as the example use case in the [[Agent skeleton = Instruction + Skills-Resources + Tools + Context]] diagram.

## Related

- [[Agent skeleton = Instruction + Skills-Resources + Tools + Context]]
