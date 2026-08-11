---
ai_hash: 626b5ddaaf93c64a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-26
entities: []
source: session 2026-06-26
status: seedling
tags:
- klara
- earchive
- playwright
- jsf
- gotcha
- benchmark
title: JSF LetterStorageDetail instance URL dies mid-benchmark; remint via eArchive
  menu
type: lesson
---

# JSF LetterStorageDetail instance URL dies mid-benchmark; remint via eArchive menu

During multi-run Playwright benchmarks on dev.klara.tech eArchive, a drilled folder's JSF detail-instance URL (`.../instances/luz_epost_business_web$1/<ID>/ch.klara.epostbusiness.LetterStorageDetail/LetterStorageDetail.xhtml`) periodically **dies mid-run** — navigating to it lands on `ch.klara.luz.components.MessagePage` showing "Liem, something unexpected has happened". The root `LetterStorage` instance and the first drill (Toys-0) survived longer; the second/last drill (Electronics-1) is what failed — twice across cases (a_vu run 3, liem run 6). A transient MCP `navigate` error — *"Execution context was destroyed, most likely because of a navigation"* — often immediately precedes the dead instance.

**Why:** JSF instance ids are session-scoped and rotate/expire; deep-linking straight to a stale detail instance (instead of clicking through) can hit an expired/GC'd view, especially under benchmark load.

**Recovery (no re-login needed):**
1. Re-click the eArchive menu link to mint a FRESH root instance id: `document.querySelector('a.menuItem-earchive').click()`
2. Re-drill each folder via its tooltip span to re-capture fresh detail URLs: `document.querySelector('span[data-tooltip="Toys-0"]').click()` (capture URL), back to root, repeat for `Electronics-1`.
3. Continue remaining runs with the new URL set.

The re-drill traffic lands inside the same run window, so per-endpoint stats still aggregate correctly — just note it in the report.

## Related

- [[playwright-klara-earchive skill]]
- [[eArchive index benchmark suite (no_index vs a_vu vs liem)]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive perf test plan 5 scenarios, all automated by trace tool]]
- [[Trace tool folder-drill waits 3min because folder view lacks Documents-Custom counters]]
- [[Server-rendered JSF apps never expose their internal API calls to browser network capture]]
- [[eArchive page DOM selectors (performance automation)]]
- [[Perf 800k tenant eArchive reload timing]]

%% ai-graph-end %%