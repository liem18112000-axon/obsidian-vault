---
ai_hash: 43fa4c248a595f77
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-19
entities: []
source: Vinnstack QA Cockpit 2026-07-19
status: seedling
tags:
- architecture
- qa
- postgres
- event-log
- vinnstack
title: Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality
  dashboards from raw tables
type: model
---

# Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality dashboards from raw tables

When several quality features (test-impact, PR-review, triage, coverage, flakiness) each need to feed dashboards / a matrix / a release gate, do NOT let every reader re-join the raw feature tables. Introduce one normalized, append-only "signal bus" table: `(epic, story_key?, scenario_id?, kind, status, score?, summary, payload jsonb, ref_url?, created_at)`. Each feature emits ONE row on completion; readers query it, not the source tables.

Two design choices that make it work:
- **Append-only** (never update-in-place): keeping every emission, not just the current value, is what later enables trend detection ("is flakiness getting worse?") and the gate history. Same rationale as an event log / `usageLog.jsonl`.
- **Latest-per-dimension read via `SELECT DISTINCT ON (kind, story_key, scenario_id) ... ORDER BY kind, story_key, scenario_id, created_at DESC`** — collapses the history to the current state of each dimension in one query. (Postgres DISTINCT ON keeps the first row per group after the ORDER BY.)

Emit must be best-effort / non-fatal (`void emitSignal(...)`, swallow errors) so a signal write never breaks the feature that produced it.

The payoff: point-features become a coherent product — the traceability matrix and release gate read ONE table with a uniform `status` (ok/warn/fail/info) instead of N bespoke joins. Built as the backbone of the Vinnstack QA Cockpit (`lib/qa/signalBus.ts`, doc/qa-cockpit-design.md).

Related: [[Self-migrating Postgres tables via memoized CREATE TABLE IF NOT EXISTS]] — the bus self-migrates the same way.

## Related

- [[Self-migrating Postgres tables via memoized CREATE TABLE IF NOT EXISTS]]

%% ai-graph-start %%

**Related notes:**
- [[Per-key write lock for parallel aggregate writes; self-migrating column via idempotent ALTER]]
- [[Keep the release gate deterministic; put AI judgment in upstream signals not the gate]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]
- [[Batch multi-row INSERTs to cut round-trips on aggregate saves (Postgres)]]
- [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]

%% ai-graph-end %%