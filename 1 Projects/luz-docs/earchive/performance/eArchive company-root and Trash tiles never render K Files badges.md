---
ai_hash: a340d05a3711a5ce
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- eArchive
- company-root tile
- Trash tile
- K Files badges
- Books-0
- Home-1
- LiemCompany
- trace-earchive.js
- collectPageEnter
- Trace tool folder-drill
- Documents-Custom counters
source: trace run 2026-07-21
status: seedling
tags:
- earchive
- playwright
- gotcha
title: eArchive company-root and Trash tiles never render K Files badges
type: observation
---

# eArchive company-root and Trash tiles never render K Files badges

On the eArchive root view only real custom folders (Books-0, Home-1) render a "K Files" badge — the company-root tile (LiemCompany) and Trash never do (no badge, not even a skeleton). Any completeness check of the form "badges parsed >= folder rows seen" is therefore structurally unreachable on the root view and burns its whole poll timeout. This also explains the earlier observation that only 2 of 4 root folder badges were ever captured.

Fixed in trace-earchive.js collectPageEnter by adding a settle fallback: page-enter records count as complete when the required marks are in AND either every row parsed OR the recorder produced no new data for 8 s.

## Related

- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]

%% ai-graph-start %%

**Related notes:**
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[eArchive count skeleton CSS classes are the reliable counts-loaded signal]]
- [[Dev eArchive baseline items in 6s but count badges take 22-41s]]
- [[eArchive counter metrics timed from page-load start to skeleton replacement]]
- [[Materialize gate cache never latches, hammers campaign service on every count]]

**Relations:**
- company-root tile — *does not render* — K Files badges
- Trash tile — *does not render* — K Files badges
- Books-0 — *renders* — K Files badges
- Home-1 — *renders* — K Files badges
- company-root tile — *is* — LiemCompany
- collectPageEnter — *is function in* — trace-earchive.js
- eArchive — *issue fixed by* — collectPageEnter
- eArchive — *is related to* — Trace tool folder-drill
- Trace tool folder-drill — *lacks* — Documents-Custom counters

%% ai-graph-end %%