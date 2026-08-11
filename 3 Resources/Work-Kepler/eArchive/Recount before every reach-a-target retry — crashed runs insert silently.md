---
ai_hash: de4890ef5fd841b5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-10
entities: []
source: session 2026-07-10
status: seedling
tags:
- earchive
- mongodb
- skill-gotcha
- recovery
title: Recount before every reach-a-target retry — crashed runs insert silently
type: lesson
---

# Recount before every reach-a-target retry — crashed runs insert silently

When using the earchive-data-prepare reach-a-target pattern (`APPEND=true FOLDER_COUNT=0 DOC_COUNT=<target - existing>`) to recover from a crashed seed, the existing-document count must be re-read **immediately before each retry**, not reused from an earlier check — a crashed run can silently insert several batches before it dies.

Concretely: a run crashed with `ECONNREFUSED` right after logging `reusing existing folders: 20`, with zero visible doc-insert log lines (per [[earchive-data-prepare logs document progress only every 10 batches]], those only print every 10 batches). It looked like it inserted nothing. In fact when the *next* retry started, its own `pre: folders=20 documents=8394` line revealed ~7,000 docs had gone in silently before the crash — jumping from a previously-counted 1,394. Using the stale 1,394-based remainder would have overshot the 20,000 target by thousands of docs.

The fix applied: stop the run immediately once its `pre:` line reveals a higher-than-expected count, kill any leftover `kubectl port-forward` processes it spawned (they don'\''t always die with the parent), recount folders/documents fresh via a throwaway port-forward + `countDocuments()`, then relaunch with the freshly computed remainder. Trust the script'\''s own `pre:` log line over any count you cached earlier in the conversation.

## Related

- [[Resume a partial earchive seed with APPEND instead of re-truncating]]
- [[earchive-data-prepare logs document progress only every 10 batches]]
- [[earchive-prepare-knobs]]

%% ai-graph-start %%

**Related notes:**
- [[Resume a partial earchive seed with APPEND instead of re-truncating]]
- [[earchive-data-prepare wrapper exits 0 even when the generator dies mid-run (verify the log footer)]]
- [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]
- [[earchive-data-prepare logs document progress only every 10 batches]]
- [[Materialize gate must require _shard or parallelized count undercounts]]

%% ai-graph-end %%