---
title: "eArchive company-root and Trash tiles never render K Files badges"
created: 2026-07-21
type: observation
status: seedling
source: "trace run 2026-07-21"
tags: [earchive, playwright, gotcha]
---

# eArchive company-root and Trash tiles never render K Files badges

On the eArchive root view only real custom folders (Books-0, Home-1) render a "K Files" badge — the company-root tile (LiemCompany) and Trash never do (no badge, not even a skeleton). Any completeness check of the form "badges parsed >= folder rows seen" is therefore structurally unreachable on the root view and burns its whole poll timeout. This also explains the earlier observation that only 2 of 4 root folder badges were ever captured.

Fixed in trace-earchive.js collectPageEnter by adding a settle fallback: page-enter records count as complete when the required marks are in AND either every row parsed OR the recorder produced no new data for 8 s.

## Related

- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
