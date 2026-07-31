---
ai_hash: bf3cd612cc364375
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- Dev eArchive
- items
- count badges
- trace tool
- FCP
- LCP
- CLS
- TBT
- Custom (4) folder count
- Per-folder "999+ Files" badges
- Documents (98865) total count
- Scroll load-more
- File-details popup
- empty folder (Accounting)
- sub-folder "0 Files" badges
- eArchive perf test plan 5 scenarios
- slow count path
- materialize gate
- count fanout work
- dev.klara.tech
- LiemCompany
- 98 865 docs
- page usable
- all counters' skeletons
- eArchive click
- trace tool run 2026-07-21
source: trace run 2026-07-21
status: seedling
tags:
- earchive
- performance
- baseline
- dev
title: 'Dev eArchive baseline: items in 6s but count badges take 22-41s'
type: observation
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

%% ai-graph-start %%

**Related notes:**
- [[eArchive counter metrics timed from page-load start to skeleton replacement]]
- [[Perf 800k tenant eArchive reload timing]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[eArchive company-root and Trash tiles never render K Files badges]]

**Relations:**
- Dev eArchive — *has baseline from* — trace tool run 2026-07-21
- Dev eArchive — *is hosted at* — dev.klara.tech
- Dev eArchive — *is associated with* — LiemCompany
- Dev eArchive — *contains* — 98 865 docs
- eArchive click — *leads to* — page usable
- page usable — *takes* — 7.4 s
- page usable — *displays* — first 47 items
- page usable — *displays* — all counters' skeletons
- all counters' skeletons — *appear at* — ~6.0 s
- eArchive click — *has FCP of* — 2.7 s
- eArchive click — *has LCP of* — 7.9 s
- eArchive click — *has CLS of* — 0.02
- eArchive click — *has TBT of* — 66 ms
- Custom (4) folder count — *takes* — 6.0 s
- Per-folder "999+ Files" badges — *take* — 22.2 s
- Documents (98865) total count — *takes* — 31.0 s
- Documents (98865) total count — *is* — slowest widget
- Documents (98865) total count — *is symptom of* — slow count path
- slow count path — *involves* — materialize gate
- slow count path — *involves* — count fanout work
- Scroll load-more — *adds* — +48 items
- Scroll load-more — *batch complete takes* — 7.1 s
- File-details popup — *takes* — 0.4 s
- items — *in empty folder (Accounting) take* — 0.2 s
- sub-folder "0 Files" badges — *in empty folder (Accounting) take* — 27–41 s
- items — *rendering is* — fast
- count badges — *are the* — bottleneck
- count badges — *take* — 22–41 s
- Documents (98865) total count — *is* — worst
- Documents (98865) total count — *takes* — 31 s
- Dev eArchive — *is related to* — eArchive perf test plan 5 scenarios
- eArchive perf test plan 5 scenarios — *automated by* — trace tool

%% ai-graph-end %%