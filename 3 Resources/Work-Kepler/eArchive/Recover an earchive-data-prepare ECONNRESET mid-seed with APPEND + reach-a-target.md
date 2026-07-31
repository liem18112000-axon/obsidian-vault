---
title: "Recover an earchive-data-prepare ECONNRESET mid-seed with APPEND + reach-a-target"
created: 2026-07-10
type: lesson
status: seedling
source: "session 2026-07-10"
tags: [earchive, mongodb, skill-gotcha, kubectl, recovery]
---

# Recover an earchive-data-prepare ECONNRESET mid-seed with APPEND + reach-a-target

A running earchive-data-prepare seed can die mid-generation with `[prepare] fatal: read ECONNRESET` when the kubectl port-forward to the Mongo primary drops — this happened after folders were inserted but only ~1,400/20,000 docs were in.

Recovery doesn'\''t require restarting from scratch. Since folders were already generated (and folder generation is fast/idempotent to redo), the fix is:
1. Spin up a throwaway `kubectl port-forward` to the confirmed primary (from the crashed run'\''s own log line, e.g. `luz-mongodb00-cluster-rs-2`) and run a one-off `countDocuments()` on `folders` and `documents` to see exactly how much survived.
2. Re-invoke `prepare.sh` with `APPEND=true FOLDER_COUNT=0 DOC_COUNT=<target - existing documents>` — `APPEND` skips the truncate, `FOLDER_COUNT=0` reuses the existing folders instead of adding more, and the doc count is set to just the remainder needed to reach the original target.

This is the same [[earchive-prepare-knobs]] reach-a-target pattern, just triggered by a crash instead of a deliberate incremental add — the pattern is crash-recovery-safe because inserts are unordered and partial generation is explicitly documented as recoverable by re-running.

## Related

- [[earchive-prepare-knobs]]
- [[earchive-data-prepare logs document progress only every 10 batches]]
