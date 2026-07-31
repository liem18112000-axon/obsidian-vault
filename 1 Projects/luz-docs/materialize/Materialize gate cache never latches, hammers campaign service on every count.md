---
title: "Materialize gate cache never latches, hammers campaign service on every count"
created: 2026-07-22
type: observation
status: seedling
source: "session 2026-07-22, performance.klara.tech test run"
tags: [luz-docs, materialize, performance, gate, gcp-logging]
---

# Materialize gate cache never latches, hammers campaign service on every count

Confirmed on performance.klara.tech (image a396919494, 2026-07-22): the eArchive page's "Documents (N)" total count never resolved within 90s, and per-folder "K Files" badges took ~62-64s to resolve, for tenant 45b05710-b9d4-4d3e-935e-83c4525369fa (LiemCompany).

Root cause evidence from GKE logs (luz-docs, namespace performance) during the test window (10:52:30-10:59:00Z): that ONE tenant made 264 (~132 distinct request/response log-line pairs) calls to `POST .../luz_docs_migration_campaign?sort=...` (the MigrationCampaignService.findBySubject call backing MaterializeGate.isMaterializationComplete), spread evenly across the full ~5.5 minute session (roughly one call every 2-3s) -- while every OTHER tenant in the same window only called it ~4 times (a background materializeCascade cron doing a one-off check per tenant). Individual campaign-lookup calls were fast (avg 23ms, max ~457ms) -- this isn't one slow query, it's a poll storm: something on the count/badge path re-hits the campaign service on every poll tick instead of caching the result once per gate check.

This matches the pre-existing known issue in [[Materialize gate cache ineffective]] -- DualCache never latches for isMaterializationComplete, so every count call pays a fresh campaign read. The fix (LUZ-157705 e-archive cronjob, commit a396919494) that was just rolled out did NOT touch this cache path, so the slowdown persists after deploy -- it's a separate, already-tracked optimize-gate item, not a regression from this rollout.

Diagnostic technique worth repeating: when a specific UI element hangs, grep GKE logs for `textPayload:"<the specific slow endpoint>"` bounded to the exact test's absolute timestamp window (not FRESHNESS, which is relative-to-now and gets swamped by newer noise on a high-traffic perf cluster -- e.g. this cluster emits ~1300 log lines/sec, so a 3000-entry limit only reached back 2.3 seconds), then group hits by tenant/id in the URL path to spot which tenant is hammering an endpoint.

## Related

- [[Materialize gate cache ineffective]]
- [[MaterializeGate migration check falls through to repo on missing campaign]]
