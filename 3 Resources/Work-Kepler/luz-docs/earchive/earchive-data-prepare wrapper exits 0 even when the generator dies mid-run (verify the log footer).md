---
ai_hash: 6ff3036265f86797
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-23
entities: []
source: session 2026-06-23 eArchive seed
status: seedling
tags:
- earchive
- luz-docs
- seeding
- gotcha
- kubectl
- port-forward
title: earchive-data-prepare wrapper exits 0 even when the generator dies mid-run
  (verify the log footer)
type: lesson
---

# earchive-data-prepare wrapper exits 0 even when the generator dies mid-run (verify the log footer)

A background `prepare.sh` (earchive-data-prepare) run can report **exit 0 even when the Node generator failed partway**. Seen 2026-06-23: the kubectl port-forward dropped mid-run (`[prepare] fatal: connect ECONNREFUSED ::1:27017 ... generator exited with 1`) at 10k/130k docs, yet the outer task notification said "completed (exit code 0)". The wrapper's exit status does not propagate the generator's failure.

**So never trust the task exit code as proof of a successful seed.** Confirm success by:
1. Tailing the log for the footer lines `[prepare] post-generate: folders=.. documents=..` and `[prepare] total elapsed: ..s` (absent = it died early), and checking for a `fatal:` / `generator exited with 1` line, then
2. Running an exact count and confirming it equals the target.

**Recovery:** the failure is usually a transient port-forward drop, not data corruption. In truncate mode just re-run — it wipes the partial rows and regenerates the exact target cleanly.

Relates to [[directConnection=true counts read only the connected node and can be stale on a secondary]].

## Related

- [[directConnection=true counts read only the connected node and can be stale on a secondary]]

%% ai-graph-start %%

**Related notes:**
- [[Recount before every reach-a-target retry — crashed runs insert silently]]
- [[Long real-API seed aborts on socket hang up unless port-forward reconnects]]
- [[Resume a partial earchive seed with APPEND instead of re-truncating]]
- [[earchive-data-prepare logs document progress only every 10 batches]]
- [[earchive-data-prepare seeds all-public docs when a tenant has no security classes (CODE_POOL empty)]]

%% ai-graph-end %%