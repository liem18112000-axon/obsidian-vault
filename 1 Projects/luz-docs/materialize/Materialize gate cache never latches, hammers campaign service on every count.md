---
ai_hash: 0ac07b84eb34a441
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- Materialize gate cache
- campaign service
- performance.klara.tech
- eArchive page
- Documents (N) total count
- K Files badges
- tenant 45b05710-b9d4-4d3e-935e-83c4525369fa
- LiemCompany
- GKE logs
- luz-docs
- namespace performance
- luz_docs_migration_campaign endpoint
- MigrationCampaignService.findBySubject
- MaterializeGate.isMaterializationComplete
- materializeCascade cron
- DualCache
- LUZ-157705 e-archive cronjob
- commit a396919494
- Materialize gate cache ineffective
- MaterializeGate migration check falls through to repo on missing campaign
- cache path
- poll storm
- gate check
source: session 2026-07-22, performance.klara.tech test run
status: seedling
tags:
- luz-docs
- materialize
- performance
- gate
- gcp-logging
title: Materialize gate cache never latches, hammers campaign service on every count
type: observation
---

# Materialize gate cache never latches, hammers campaign service on every count

Confirmed on performance.klara.tech (image a396919494, 2026-07-22): the eArchive page's "Documents (N)" total count never resolved within 90s, and per-folder "K Files" badges took ~62-64s to resolve, for tenant 45b05710-b9d4-4d3e-935e-83c4525369fa (LiemCompany).

Root cause evidence from GKE logs (luz-docs, namespace performance) during the test window (10:52:30-10:59:00Z): that ONE tenant made 264 (~132 distinct request/response log-line pairs) calls to `POST .../luz_docs_migration_campaign?sort=...` (the MigrationCampaignService.findBySubject call backing MaterializeGate.isMaterializationComplete), spread evenly across the full ~5.5 minute session (roughly one call every 2-3s) -- while every OTHER tenant in the same window only called it ~4 times (a background materializeCascade cron doing a one-off check per tenant). Individual campaign-lookup calls were fast (avg 23ms, max ~457ms) -- this isn't one slow query, it's a poll storm: something on the count/badge path re-hits the campaign service on every poll tick instead of caching the result once per gate check.

This matches the pre-existing known issue in [[Materialize gate cache ineffective]] -- DualCache never latches for isMaterializationComplete, so every count call pays a fresh campaign read. The fix (LUZ-157705 e-archive cronjob, commit a396919494) that was just rolled out did NOT touch this cache path, so the slowdown persists after deploy -- it's a separate, already-tracked optimize-gate item, not a regression from this rollout.

Diagnostic technique worth repeating: when a specific UI element hangs, grep GKE logs for `textPayload:"<the specific slow endpoint>"` bounded to the exact test's absolute timestamp window (not FRESHNESS, which is relative-to-now and gets swamped by newer noise on a high-traffic perf cluster -- e.g. this cluster emits ~1300 log lines/sec, so a 3000-entry limit only reached back 2.3 seconds), then group hits by tenant/id in the URL path to spot which tenant is hammering an endpoint.

## Related

- [[Materialize gate cache ineffective]]
- [[MaterializeGate migration check falls through to repo on missing campaign]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive 800k bottleneck is view-controller not K]]
- [[eArchive count baseline latency on dev ~80s for 128k docs (fan-out off)]]
- [[Migration campaign status can silently drift from real document state]]
- [[eArchive request flow and log correlation (perf)]]
- [[luz-docs documentscount is ~130s on an 800k tenant — the 16-shard fan-out, not counting, is the bottleneck]]

**Relations:**
- Materialize gate cache — *never latches* — campaign service
- Materialize gate cache — *hammers* — campaign service
- performance.klara.tech — *confirms issue on* — eArchive page
- eArchive page — *displays* — Documents (N) total count
- eArchive page — *displays* — K Files badges
- tenant 45b05710-b9d4-4d3e-935e-83c4525369fa — *is also known as* — LiemCompany
- tenant 45b05710-b9d4-4d3e-935e-83c4525369fa — *calls* — luz_docs_migration_campaign endpoint
- luz_docs_migration_campaign endpoint — *is backed by* — MigrationCampaignService.findBySubject
- MigrationCampaignService.findBySubject — *is part of* — MaterializeGate.isMaterializationComplete
- MaterializeGate.isMaterializationComplete — *is a* — gate check
- MaterializeGate.isMaterializationComplete — *is checked by* — Materialize gate cache
- Materialize gate cache — *uses* — DualCache
- DualCache — *never latches for* — MaterializeGate.isMaterializationComplete
- GKE logs — *are sourced from* — luz-docs
- luz-docs — *runs in* — namespace performance
- Materialize gate cache — *is linked to known issue* — Materialize gate cache ineffective
- LUZ-157705 e-archive cronjob — *has commit* — a396919494
- LUZ-157705 e-archive cronjob — *did not affect* — cache path
- MaterializeGate migration check falls through to repo on missing campaign — *is related to* — Materialize gate cache
- materializeCascade cron — *performs one-off check per* — tenant
- Documents (N) total count — *failed to resolve* — within 90s
- K Files badges — *took long to resolve* — ~62-64s
- Materialize gate cache — *causes* — poll storm
- poll storm — *targets* — campaign service
- cache path — *is for* — Materialize gate cache
- Materialize gate cache — *is a* — cache
- campaign service — *is a* — service
- luz_docs_migration_campaign endpoint — *is an* — endpoint
- MigrationCampaignService.findBySubject — *is a* — service call
- materializeCascade cron — *is a* — cron job
- LUZ-157705 e-archive cronjob — *is a* — cron job
- Materialize gate cache ineffective — *is a* — known issue
- MaterializeGate migration check falls through to repo on missing campaign — *is a* — related issue

%% ai-graph-end %%