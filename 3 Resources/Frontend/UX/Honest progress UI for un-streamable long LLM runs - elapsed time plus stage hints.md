---
ai_hash: 48f811a9fb9f86c1
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03, vinnstack CreatingEpicPanel
status: seedling
tags:
- ux
- loading
- llm
- react
title: Honest progress UI for un-streamable long LLM runs - elapsed time plus stage
  hints
type: model
---

# Honest progress UI for un-streamable long LLM runs - elapsed time plus stage hints

When a UI action triggers ONE long opaque LLM run (minutes, single POST, no streamable progress), do not fake a percent bar - it lies and stalls at 90%. Compose honest indicators instead:

1. **Elapsed clock** (mm:ss) - the only true number you have.
2. **Stage hints derived from elapsed time** ("fetching from Jira..." < 8s, "grounding in the codebase..." < 45s, "drafting questions..." < 150s, then "still working - big inputs take minutes"). They describe the known pipeline shape without claiming precision, and the last stage absorbs overruns gracefully.
3. **Skeletons shaped like the outcome** (question-card skeletons, not generic spinners) - primes the user for what will appear.
4. **Indeterminate pulse bar** for motion, **Stop always visible**, and failure rendered in-place with a Dismiss (not a toast the user misses).

Also reflect the pending item where it will eventually live (a spinner card in the list/inbox), so the action visibly "took" immediately. Clear the pending state on success in the data handler; on error KEEP it and show the error where the user is looking.

## Related

- [[Acknowledgment checkboxes must reset every time their dialog reopens]]

%% ai-graph-start %%

**Related notes:**
- [[Client-side generation queue lets independent items run without blocking each other]]
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
- [[Measure infinite-scroll load-more by baseline count then poll until stable]]
- [[Set HTTPserverless maxDuration above the internal LLM-run timeout, not below]]

%% ai-graph-end %%