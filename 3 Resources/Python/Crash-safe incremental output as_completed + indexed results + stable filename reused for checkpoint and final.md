---
title: "Crash-safe incremental output: as_completed + indexed results + stable filename reused for checkpoint and final"
created: 2026-06-14
type: howto
status: seedling
source: "fb-info-project fix D, 2026-06-14"
tags: [python, asyncio, resilience, pattern, fb-info-project]
---

# Crash-safe incremental output: as_completed + indexed results + stable filename reused for checkpoint and final

Pattern for making a long concurrent fan-out write a usable partial result if it is killed mid-run (implemented in fb-info-project `profiles.visit_all` + `service.batch` + `excel_io`):

1. **Stable filename, minted once.** Split the output-path computation (`output_path(source, item)` — one timestamp, fixed at call) from the file-writing (`save_template(..., path=None)`). The caller computes the path **once per item** and reuses it for every checkpoint AND the final write, so they all target the same file instead of spawning a new timestamped file each save.

2. **Stream with `as_completed`, not `gather`.** `gather` only returns when everything is done, so a kill loses all work. Iterate `for fut in asyncio.as_completed([one(i,p) for i,p in enumerate(items)])` and write a checkpoint every N completions.

3. **Preserve input order despite out-of-order completion.** Each task returns `(i, row)`; store into a pre-sized `results=[None]*len(items)` by index. The checkpoint/final output is `[r for r in results if r is not None]` — completion order doesn't scramble it (here: keeps commenters newest-first).

4. **Always flush once at the end** (after the loop), since the last batch may not land on the every-N boundary.

5. **Deferred fields.** A value not known until the run finishes (here the post `title`) is written blank in checkpoints; the final save fills it in. Acceptable for a crash-recovery partial.

Verified the checkpoint→final overwrite contract offline: same path, final write replaces the blank-title partial with the real title and full row set.

## Related

- [[fb-scraper writes output per link only at the end; killing mid-run loses the whole link]]
