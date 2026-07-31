---
title: "Honest progress UI for un-streamable long LLM runs - elapsed time plus stage hints"
created: 2026-07-03
type: model
status: seedling
source: "session 2026-07-03, vinnstack CreatingEpicPanel"
tags: [ux, loading, llm, react]
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
