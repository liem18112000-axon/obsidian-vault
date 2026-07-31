---
title: "Cloud Logging share link endTime can truncate a job's logs mid-run"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11"
tags: [gcp, cloud-logging, gotcha]
---

# Cloud Logging share link endTime can truncate a job's logs mid-run

The `endTime` baked into a Cloud Logging share link is the moment the link was created, not the end of the workload — if the job was still running, the linked window cuts off mid-stream and the final summary (e.g. behave's pass/fail tally) is missing.

Fix: after reading the linked window, run a second `gcloud logging read` with `timestamp>"<endTime>"` and the same resource/label filter to fetch the tail.

See also [[Resolve Cloud Logging share links via redirect Location header]].

## Related

- [[Resolve Cloud Logging share links via redirect Location header]]
