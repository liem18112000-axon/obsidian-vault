---
title: "Converge an exploration loop on marginal yield (zero new items), not a fixed iteration count"
created: 2026-09-03
type: lesson
status: seedling
source: "session 2026-09-03 — KGA G5 controller"
tags: [agent-design, agentic-loop, convergence, retrieval, resumable]
---

# Converge an exploration loop on marginal yield (zero new items), not a fixed iteration count

When an agent explores an unknown-size space in rounds (crawl more, search more, discover more), **stop on MARGINAL YIELD — a round that adds nothing new — not on a fixed number of iterations or a fixed depth.** A fixed count both under-explores rich cases and wastes rounds on exhausted ones; the "did this round add any new item?" signal adapts to the actual space.

Concretely, per round: derive the next query/focus from what the *previous* round discovered, run it, and dedup results against an accumulated `visited` set. Converge when the new-item count for a round is zero (and also guard with a hard bound — max rounds + a total wall-clock/token budget — so a pathological case still terminates). Check convergence at more than one point: before the expensive step (nothing new to expand) and after it (the step produced nothing new).

**Deriving the next rounds focus from discovered content is the engine of self-exploration** — e.g. take the salient tokens of the titles of nodes found this round and search on those; that surfaces related items the seed never linked (in one real run it derived "release 7.29", a term absent from the seed). Keep the derived focus TIGHT (few high-signal tokens) — broad token sets over-match ([[LLM query enrichment for a substring-OR matcher must contract, not expand, the token set]]).

**Make the loop RESUMABLE**: persist `{round, visited, focus, reflections}` keyed by a stable id (context/session id) to durable storage at each round boundary; on start, load and resume from the stored round rather than restarting. This is what lets a loop survive a redeploy / request-timeout / crash mid-exploration instead of re-doing all the expensive work. Keep the core loop cheap (no LLM per round if the focus can be derived mechanically); gate any per-round model calls behind flags and offload them so they never block the event loop.

Surfaced building the test-agent KGA self-exploration controller (G5): fan-out → crawl → reflect → derive-focus → converge, bounded + resumable, default-off. Related: [[Retrieval tiering: query knowledge sources cheapest and most-trusted first]].

## Related

- [[Retrieval tiering: query knowledge sources cheapest and most-trusted first]]
- [[LLM query enrichment for a substring-OR matcher must contract]]
- [[not expand]]
- [[the token set]]
