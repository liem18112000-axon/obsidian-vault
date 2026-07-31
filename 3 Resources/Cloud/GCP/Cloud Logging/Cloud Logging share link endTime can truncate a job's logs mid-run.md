---
ai_hash: 3c4ab8d66cac79ed
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11
status: seedling
tags:
- gcp
- cloud-logging
- gotcha
title: Cloud Logging share link endTime can truncate a job's logs mid-run
type: lesson
---

# Cloud Logging share link endTime can truncate a job's logs mid-run

The `endTime` baked into a Cloud Logging share link is the moment the link was created, not the end of the workload — if the job was still running, the linked window cuts off mid-stream and the final summary (e.g. behave's pass/fail tally) is missing.

Fix: after reading the linked window, run a second `gcloud logging read` with `timestamp>"<endTime>"` and the same resource/label filter to fetch the tail.

See also [[Resolve Cloud Logging share links via redirect Location header]].

## Related

- [[Resolve Cloud Logging share links via redirect Location header]]

%% ai-graph-start %%

**Related notes:**
- [[Resolve Cloud Logging share links via redirect Location header]]
- [[GKE log FRESHNESS window gets swamped by noise on high-traffic clusters]]

%% ai-graph-end %%