---
ai_hash: 30b2d494503cf390
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: 'fb-info-project PR #8, session 2026-06-30'
status: seedling
tags:
- gotcha
- accounting
- dedup
- fb-info-project
title: Row-per-event output breaks row-count quota accounting
type: lesson
---

# Row-per-event output breaks row-count quota accounting

When a collector is changed from **dedup-by-entity** to **one-row-per-event**, any downstream accounting that counts rows (`len(rows)`) silently breaks — and the breakage is invisible until quotas behave wrongly.

## The trap
A FB comment scraper used to emit one row per *commenter* (deduped). It was changed to emit one row per *comment/reply* to preserve the full reply tree. Output now contains duplicate `profile_url`s. But the license **budget** (`budget -= len(rows)`) and the **`--max-profiles` cap** (`profiles[:n]`) still counted rows. Result: a person who replies 5× was charged as 5 profile-visits, over-spending the license and stopping runs early; and the cap truncated to fewer *distinct* profiles than asked.

## Why it is wrong
The real cost unit is the **distinct entity actually fetched**, not the row. `visit_all` coalesces fetches by `profile_url` (each profile fetched once even if it appears in many rows), so cost ≠ row count.

## Fix
Charge and cap by distinct key:
- `unique_profiles(rows)` → `len({r["profile_url"] for r in rows})` for budget + `grant.commit`.
- `cap_to_profiles(profiles, cap)` → keep every row of the newest N *distinct* profiles.

## General lesson
Whenever output cardinality decouples from cost cardinality (dedup, coalescing, caching, fan-out), audit every `len(...)` used for quota/billing/caps — count the cost unit, not the row.

Context: fb-info-project `src/service.py`, PR #8.

%% ai-graph-start %%

**Related notes:**
- [[Rate-limit a Facebook scraper by profiles-per-day, not lifetime total]]
- [[--max-expand caps comment batches not profile count; profile-visit phase dominates runtime]]
- [[fb-scraper writes output per link only at the end; killing mid-run loses the whole link]]
- [[A resume must not re-charge one-time accounting]]
- [[Distinguish absent control from missed click when expanding lazy lists]]

%% ai-graph-end %%