---
ai_hash: 2b3211740ca96461
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities:
- interrogate-qa skill
- Vinnstack
- Interrogation Room UI
- business track
- technical track
- db/schema.sql
- questions table
- CHECK constraint
- schema migration
- prompt file
- tooling
- agents
- app
- persistence layer
- UI/track system
- standalone tool
- app wiring
- Epics
- Stories
- Flows
- vinnstack-skills/interrogate-qa/SKILL.md
- QA track
- new enum value
- UI wiring
- DB CHECK constraint
- new kind of interrogation
source: session 2026-07-11 — creating interrogate-qa skill
status: seedling
tags:
- vinnstack
- ai-first-pipeline
- schema-constraint
- skill-design
title: interrogate-qa skill built standalone due to track CHECK constraint
type: lesson
---

# interrogate-qa skill built standalone due to track CHECK constraint

Vinnstack's new `interrogate-qa` skill (vinnstack-skills/interrogate-qa/SKILL.md) was deliberately built as a standalone, manually-invoked skill rather than wired into the Interrogation Room UI as a third "track" alongside business/technical.

Reason: `db/schema.sql`'s `questions` table has a hard constraint `track TEXT NOT NULL CHECK (track IN ('business','technical'))`. Adding a QA track would need a schema migration (new enum value + UI wiring in the Interrogation Room), which was out of scope for "just create a skill". So the skill exists as a prompt file other tooling/agents can invoke directly, without being surfaced as a tab in the app's Interrogation Room.

General lesson: when a new "kind" of interrogation/question-set is requested but the persistence layer enumerates allowed kinds via a DB CHECK constraint, check that constraint before assuming the new kind can just plug into the existing UI/track system — it may need to ship as a standalone tool first, with app wiring as a separate, explicitly-scoped follow-up.

## Related
[[interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]

## Related

- [[1 Projects/Vinnstack/interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]

%% ai-graph-start %%

**Related notes:**
- [[interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]
- [[Technical interrogation questions must pass the 2-minute tech-lead test]]
- [[Vinnstack vinnstack-data-model.html predates the BDD workspace]]
- [[A feedback loop with only its write side wired looks like a broken feature]]
- [[Migrating Vinnstack Interrogation Room from JSON files to normalized Postgres (design)]]

**Relations:**
- interrogate-qa skill — *is_part_of* — Vinnstack
- interrogate-qa skill — *is_defined_in* — vinnstack-skills/interrogate-qa/SKILL.md
- interrogate-qa skill — *is_built_as* — standalone, manually-invoked skill
- interrogate-qa skill — *is_not_integrated_into* — Interrogation Room UI
- Interrogation Room UI — *has_track* — business track
- Interrogation Room UI — *has_track* — technical track
- questions table — *is_defined_in* — db/schema.sql
- questions table — *has_constraint* — CHECK constraint
- CHECK constraint — *restricts_track_to* — business track
- CHECK constraint — *restricts_track_to* — technical track
- QA track — *would_require* — schema migration
- schema migration — *involves* — new enum value
- schema migration — *involves* — UI wiring
- interrogate-qa skill — *exists_as* — prompt file
- prompt file — *can_be_invoked_by* — tooling
- prompt file — *can_be_invoked_by* — agents
- interrogate-qa skill — *is_not_surfaced_in* — app
- app — *has_component* — Interrogation Room UI
- persistence layer — *uses* — DB CHECK constraint
- DB CHECK constraint — *enumerates* — allowed kinds
- new kind of interrogation — *may_need_to_be* — standalone tool
- standalone tool — *requires* — app wiring
- app wiring — *is_a* — separate, explicitly-scoped follow-up
- interrogate-qa skill — *is_cross_cutting_across* — Epics
- interrogate-qa skill — *is_cross_cutting_across* — Stories
- interrogate-qa skill — *is_cross_cutting_across* — Flows

%% ai-graph-end %%