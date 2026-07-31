---
title: "Append-only QA signal bus with DISTINCT ON latest-per-dimension decouples quality dashboards from raw tables"
created: 2026-07-19
type: model
status: seedling
source: "Vinnstack QA Cockpit 2026-07-19"
tags: [architecture, qa, postgres, event-log, vinnstack]
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
