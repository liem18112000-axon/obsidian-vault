---
ai_hash: 782fecb9bbaf2849
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
aliases:
- Daily earnings digest
- Accesstrade daily report
created: 2026-06-11
entities:
- automated daily conversion digest
- Claude
- conversions
- approved revenue
- pending revenue
- top-earning content by sub1
- phone
- reporting API
- passive dashboard
- OS scheduler
- schedule skill
- Claude session
- accesstrade skill
- Accesstrade report API
- Accesstrade
- digest
- Notification hook
- Telegram
- Slack
- Zalo
- cron
- loop skill
- trailing-24h window
- 7d window
- Accesstrade API
- rate limits
- pagination
- biggest single sale
- newly-rejected conversions
- Stop hook
- periodBase = UPDATED_DATE
- pending sales
- 'context: fork'
- subagent
- main thread
- Weekly EPC-by-content report
- Alert-only mode
- threshold
- 1M VND/day
- rejection spike
- Accesstrade conversion and transaction reporting
- Accesstrade API rate limits and pagination
- Claude Code hooks event model
- Accesstrade SubID attribution
- Accesstrade API Integration - MOC
- Designing an Accesstrade skill for Claude Code
- 1-req/5-min, 7-day limit
source: research session 2026-06-11
status: seedling
tags:
- affiliate
- accesstrade
- use-case
- automation
- reporting
title: Use case - automated daily conversion digest
type: howto
---

# Use case - automated daily conversion digest

**Goal: every morning, Claude pulls yesterday's conversions, separates approved vs pending revenue, ranks the top-earning content by `sub1`, and sends a one-paragraph digest to your phone.** This turns the [[Accesstrade conversion and transaction reporting|reporting API]] into a passive dashboard you never open.

## Flow

```mermaid
flowchart TD
    Cron[OS scheduler / schedule skill] --> Sess[Launch Claude session]
    Sess --> Skill[accesstrade skill: conversions --since 1d]
    Skill --> API[(Accesstrade report API)]
    API --> Agg["Group by sub1,<br/>sum approved vs pending"]
    Agg --> Draft[Claude writes digest]
    Draft --> Notify[Notification hook -> Telegram/Slack]
```

## Build recipe

1. **Schedule** a daily Claude run (cron / the `schedule` or `loop` skill).
2. The [[Designing an Accesstrade skill for Claude Code|accesstrade skill]] fetches a trailing-24h (or 7d) window — well within the [[Accesstrade API rate limits and pagination|1-req/5-min, 7-day]] limit.
3. Claude aggregates: total approved, total pending, top 5 `sub1` by reward, biggest single sale, any newly-rejected conversions.
4. A **`Notification` or `Stop` hook** forwards the digest to Telegram/Slack/Zalo (installers already exist in this vault).

## Why it works well

- **Incremental:** use `periodBase = UPDATED_DATE` so you also catch yesterday's pending sales that flipped to approved today.
- **Cheap context:** run the pull in a `context: fork` subagent; only the finished digest returns to your main thread.
- **Honest numbers:** approved and pending are reported separately so you never celebrate revenue a merchant later cancels.

## Variations

- Weekly EPC-by-content report (revenue ÷ clicks per `sub1`).
- Alert-only mode: stay silent unless a threshold (e.g. >1M VND/day or a rejection spike) is crossed.

## Related

- [[Accesstrade conversion and transaction reporting]]
- [[Accesstrade API rate limits and pagination]]
- [[3 Resources/AI/Claude-Code/Hooks/Claude Code hooks event model]]
- [[Accesstrade SubID attribution]]
- [[Accesstrade API Integration - MOC]]

%% ai-graph-start %%

**Related notes:**
- [[Designing an Accesstrade skill for Claude Code]]
- [[Accesstrade API Integration - MOC]]
- [[Accesstrade postback and S2S conversion tracking]]
- [[Use case - campaign discovery and datafeed content briefs]]
- [[Use case - bulk tracking link generation]]

**Relations:**
- automated daily conversion digest — *is a* — Use case
- Claude — *pulls* — conversions
- Claude — *separates* — approved revenue
- Claude — *separates* — pending revenue
- Claude — *ranks* — top-earning content by sub1
- Claude — *sends* — digest
- digest — *to* — phone
- reporting API — *becomes* — passive dashboard
- OS scheduler — *initiates* — Claude session
- schedule skill — *initiates* — Claude session
- Claude session — *launches* — accesstrade skill
- accesstrade skill — *uses* — Accesstrade report API
- Accesstrade report API — *provides* — conversions
- conversions — *grouped by* — sub1
- conversions — *summed for* — approved revenue
- conversions — *summed for* — pending revenue
- Claude — *writes* — digest
- digest — *sent via* — Notification hook
- Notification hook — *sends to* — Telegram
- Notification hook — *sends to* — Slack
- Notification hook — *sends to* — Zalo
- cron — *schedules* — Claude run
- schedule skill — *schedules* — Claude run
- loop skill — *schedules* — Claude run
- accesstrade skill — *fetches* — trailing-24h window
- accesstrade skill — *fetches* — 7d window
- Accesstrade API — *has* — rate limits
- Accesstrade API — *has* — pagination
- Accesstrade API — *has limit* — 1-req/5-min, 7-day limit
- Claude — *aggregates* — approved revenue
- Claude — *aggregates* — pending revenue
- Claude — *aggregates* — top-earning content by sub1
- Claude — *aggregates* — biggest single sale
- Claude — *aggregates* — newly-rejected conversions
- Notification hook — *forwards* — digest
- Stop hook — *forwards* — digest
- digest — *forwarded to* — Telegram
- digest — *forwarded to* — Slack
- digest — *forwarded to* — Zalo
- periodBase = UPDATED_DATE — *catches* — pending sales
- context: fork — *runs* — pull
- subagent — *runs* — pull
- finished digest — *returns to* — main thread
- approved revenue — *reported separately from* — pending revenue
- Weekly EPC-by-content report — *is a* — Variation
- Alert-only mode — *is a* — Variation
- Alert-only mode — *triggers on* — threshold
- threshold — *is* — 1M VND/day
- threshold — *is* — rejection spike
- automated daily conversion digest — *related to* — Accesstrade conversion and transaction reporting
- automated daily conversion digest — *related to* — Accesstrade API rate limits and pagination
- automated daily conversion digest — *related to* — Claude Code hooks event model
- automated daily conversion digest — *related to* — Accesstrade SubID attribution
- automated daily conversion digest — *related to* — Accesstrade API Integration - MOC
- accesstrade skill — *described in* — Designing an Accesstrade skill for Claude Code
- Accesstrade — *has* — reporting API
- Accesstrade — *has* — Accesstrade report API
- Accesstrade — *has* — Accesstrade API
- Accesstrade report API — *is a type of* — Accesstrade API
- reporting API — *is a type of* — Accesstrade API

%% ai-graph-end %%