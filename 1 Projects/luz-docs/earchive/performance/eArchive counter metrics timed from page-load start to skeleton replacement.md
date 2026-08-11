---
ai_hash: 71ae9f60a1334786
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities:
- eArchive page
- count widgets
- K Files
- Documents (N)
- Custom (M)
- skeleton/spinner
- load time
- page load start
- skeleton replacement
- actual number
- number's value
- timing
- Per-folder counts
- 999+ Files
- UI
- slow count path
- materialize gate
- fanout work
- skeleton-to-number time
- user-facing proxy
- backend count latency
- eArchive perf test plan 5 scenarios, all automated by trace tool
source: session 2026-07-21
status: seedling
tags:
- earchive
- performance
- metrics
title: eArchive counter metrics timed from page-load start to skeleton replacement
type: concept
---

# eArchive counter metrics timed from page-load start to skeleton replacement

On the eArchive page, count widgets — per-folder "K Files", total "Documents (N)", total "Custom (M)" — first render as a skeleton/spinner. Their load time is measured from the start of the page load until the skeleton is replaced by the actual number, and the number's value is recorded alongside the timing. Per-folder counts above 999 render capped as "999+ Files", so exact values past that point aren't observable from the UI.

Matters because these counters are backed by the slow count path (materialize gate / fanout work), so skeleton-to-number time is the user-facing proxy for backend count latency.

## Related

- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]

%% ai-graph-start %%

**Related notes:**
- [[Dev eArchive baseline items in 6s but count badges take 22-41s]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[eArchive count skeleton CSS classes are the reliable counts-loaded signal]]
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[Perf 800k tenant eArchive reload timing]]

**Relations:**
- eArchive page — *contains* — count widgets
- count widgets — *include* — K Files
- count widgets — *include* — Documents (N)
- count widgets — *include* — Custom (M)
- count widgets — *render initially as* — skeleton/spinner
- load time — *measured from* — page load start
- load time — *measured to* — skeleton replacement
- skeleton replacement — *by* — actual number
- number's value — *recorded with* — timing
- Per-folder counts — *render as* — 999+ Files
- Per-folder counts — *above 999 render as* — 999+ Files
- exact values — *not observable via* — UI
- count widgets — *backed by* — slow count path
- slow count path — *involves* — materialize gate
- slow count path — *involves* — fanout work
- skeleton-to-number time — *is a* — user-facing proxy
- user-facing proxy — *for* — backend count latency
- eArchive perf test plan 5 scenarios, all automated by trace tool — *related to* — eArchive page

%% ai-graph-end %%