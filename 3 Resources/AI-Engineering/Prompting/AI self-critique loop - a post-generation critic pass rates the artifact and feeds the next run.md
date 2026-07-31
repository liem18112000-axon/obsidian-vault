---
title: "AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run"
created: 2026-07-03
type: model
status: seedling
source: "session 2026-07-03, vinnstack AI critic"
tags: [llm, self-critique, feedback-loop, vinnstack]
---

# AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run

Instead of asking the user to rate generated artifacts, run a short TOOL-LESS critic pass right after each generation: a separate prompt rates the artifact 1-5 with a <=60-word rationale (JSON out), stored with author = the model, displayed read-only next to the artifact, and injected into the NEXT regeneration of the same subject as quality feedback - a self-critique loop.

Design points that matter:
- Separate pass, not an output-contract trailer: the artifact contract stays pure ("output ONLY markdown"), and the critic can be cheap/fast (no tools, short timeout).
- Calibrate honesty in the critic prompt ("most first drafts are a 3 or 4; reserve 5 for nothing-to-improve; name the weakest part explicitly") - otherwise models grade themselves 5/5.
- Best-effort: a critic failure must never fail the generation that produced the artifact (catch + log).
- Same staleness rule as human ratings: version-stamp and only inject while the rating targets the current artifact version.

## Related

- [[Version-stamp quality ratings so stale feedback stops driving regeneration]]
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
