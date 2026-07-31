---
title: "eArchive perf test plan: 5 scenarios, all automated by trace tool"
aliases: ["eArchive perf test plan: 5 scenarios, trace tool automates only scenario 1"]
created: 2026-07-21
type: observation
status: seedling
source: "session 2026-07-21"
tags: [earchive, performance, playwright, test-plan]
---

# eArchive perf test plan: 5 scenarios, all automated by trace tool

The eArchive end-to-end performance test plan (luz_docs/docs/performance-test-800k/end-to-end-tools) defines 5 scenarios: (1) enter eArchive page, (2) scroll to load more items, (3) open file-details popup, (4) choose a non-root/non-Trash folder, (5) scroll-load-more inside that folder. The Playwright trace tool (trace-earchive.js) now automates **all five** in one browser session, reported as "test.md — N" sections in report.html.

Mechanics worth remembering:
- Scenarios 1 & 4 use an in-page recorder `window.__rec` (MutationObserver tick scans item counts, folder rows, and regex-parses "K Files" / "Documents (N)" / "Custom (M)" from rendered text, stamping first-appearance times). `window.__resetRec()` re-arms it before the folder click so folder-drill times are relative to that click; addInitScript re-injection makes this survive full navigations.
- Scroll batches (2 & 5) use `window.__scrollMark`: batch considered complete when the item count is stable for 2.5 s.
- Scenario 3 waits for any visible dialog/overlay/pdf iframe after clicking the first item.
- Output dir is `out-<env>-<timestamp>/`, env derived from URL (dev.klara.tech → dev, performance.klara.tech → perf, else first hostname label).

## Related

- [[eArchive counter metrics timed from page-load start to skeleton replacement]]
