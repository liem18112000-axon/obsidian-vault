---
title: "Dev eArchive baseline: items in 6s but count badges take 22-41s"
created: 2026-07-21
type: observation
status: seedling
source: "trace run 2026-07-21"
tags: [earchive, performance, baseline, dev]
---

# Dev eArchive baseline: items in 6s but count badges take 22-41s

Dev (dev.klara.tech, LiemCompany, ~98 865 docs) eArchive baseline from trace tool run 2026-07-21:

- eArchive click → page usable: 7.4 s (first 47 items + all counters' skeletons at ~6.0 s; FCP 2.7 s, LCP 7.9 s, CLS 0.02, TBT 66 ms)
- "Custom (4)" folder count: 6.0 s
- Per-folder "999+ Files" badges (Books-0, Home-1): 22.2 s
- "Documents (98865)" total count: **31.0 s** — slowest widget by far; user-facing symptom of the slow count path (materialize gate / count fanout work)
- Scroll load-more: +48 items, batch complete 7.1 s
- File-details popup: 0.4 s
- Inside empty folder (Accounting): items instant (0.2 s), but sub-folder "0 Files" badges trickle in at 27–41 s

Headline: item rendering is fast; count badges are the bottleneck (22–41 s), total document count worst at 31 s.

## Related

- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
