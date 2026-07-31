---
title: "interrogate-qa skill built standalone due to track CHECK constraint"
created: 2026-07-11
type: lesson
status: seedling
source: "session 2026-07-11 — creating interrogate-qa skill"
tags: [vinnstack, ai-first-pipeline, schema-constraint, skill-design]
---

# interrogate-qa skill built standalone due to track CHECK constraint

Vinnstack's new `interrogate-qa` skill (vinnstack-skills/interrogate-qa/SKILL.md) was deliberately built as a standalone, manually-invoked skill rather than wired into the Interrogation Room UI as a third "track" alongside business/technical.

Reason: `db/schema.sql`'s `questions` table has a hard constraint `track TEXT NOT NULL CHECK (track IN ('business','technical'))`. Adding a QA track would need a schema migration (new enum value + UI wiring in the Interrogation Room), which was out of scope for "just create a skill". So the skill exists as a prompt file other tooling/agents can invoke directly, without being surfaced as a tab in the app's Interrogation Room.

General lesson: when a new "kind" of interrogation/question-set is requested but the persistence layer enumerates allowed kinds via a DB CHECK constraint, check that constraint before assuming the new kind can just plug into the existing UI/track system — it may need to ship as a standalone tool first, with app wiring as a separate, explicitly-scoped follow-up.

## Related
[[interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]

## Related

- [[1 Projects/Vinnstack/interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]
