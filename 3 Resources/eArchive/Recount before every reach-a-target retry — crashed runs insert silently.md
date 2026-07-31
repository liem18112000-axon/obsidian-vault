---
title: "Recount before every reach-a-target retry — crashed runs insert silently"
created: 2026-07-10
type: lesson
status: seedling
source: "session 2026-07-10"
tags: [earchive, mongodb, skill-gotcha, recovery]
---

# Recount before every reach-a-target retry — crashed runs insert silently

When using the earchive-data-prepare reach-a-target pattern (`APPEND=true FOLDER_COUNT=0 DOC_COUNT=<target - existing>`) to recover from a crashed seed, the existing-document count must be re-read **immediately before each retry**, not reused from an earlier check — a crashed run can silently insert several batches before it dies.

Concretely: a run crashed with `ECONNREFUSED` right after logging `reusing existing folders: 20`, with zero visible doc-insert log lines (per [[earchive-data-prepare logs document progress only every 10 batches]], those only print every 10 batches). It looked like it inserted nothing. In fact when the *next* retry started, its own `pre: folders=20 documents=8394` line revealed ~7,000 docs had gone in silently before the crash — jumping from a previously-counted 1,394. Using the stale 1,394-based remainder would have overshot the 20,000 target by thousands of docs.

The fix applied: stop the run immediately once its `pre:` line reveals a higher-than-expected count, kill any leftover `kubectl port-forward` processes it spawned (they don'\''t always die with the parent), recount folders/documents fresh via a throwaway port-forward + `countDocuments()`, then relaunch with the freshly computed remainder. Trust the script'\''s own `pre:` log line over any count you cached earlier in the conversation.

## Related

- [[Recover an earchive-data-prepare ECONNRESET mid-seed with APPEND + reach-a-target]]
- [[earchive-data-prepare logs document progress only every 10 batches]]
- [[earchive-prepare-knobs]]
