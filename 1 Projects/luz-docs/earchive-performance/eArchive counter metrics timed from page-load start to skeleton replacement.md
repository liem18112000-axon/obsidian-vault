---
title: "eArchive counter metrics timed from page-load start to skeleton replacement"
created: 2026-07-21
type: concept
status: seedling
source: "session 2026-07-21"
tags: [earchive, performance, metrics]
---

# eArchive counter metrics timed from page-load start to skeleton replacement

On the eArchive page, count widgets — per-folder "K Files", total "Documents (N)", total "Custom (M)" — first render as a skeleton/spinner. Their load time is measured from the start of the page load until the skeleton is replaced by the actual number, and the number's value is recorded alongside the timing. Per-folder counts above 999 render capped as "999+ Files", so exact values past that point aren't observable from the UI.

Matters because these counters are backed by the slow count path (materialize gate / fanout work), so skeleton-to-number time is the user-facing proxy for backend count latency.

## Related

- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
