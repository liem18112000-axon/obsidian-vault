---
ai_hash: 5d0738f761dd7b89
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities:
- interrogate-qa
- Epics
- Stories
- Flows
- Vinnstack's AI-First pipeline
- interrogate-business
- interrogate-technical
- story-to-bdd-scenarios
- BDD scenarios
- QA/QC risks
- Features
- 'Background:'
- step-definition reuse
- cross-story data/sequencing dependencies
- inconsistent tagging/coverage conventions
- interrogation/review pass
- track CHECK constraint
- order-dependent, flaky BDD scenarios
- per-item skills
source: session 2026-07-11 — creating interrogate-qa skill
status: seedling
tags:
- vinnstack
- ai-first-pipeline
- test-design
- skill-design
title: interrogate-qa is cross-cutting across all Epics, Stories, and Flows
type: concept
---

# interrogate-qa is cross-cutting across all Epics, Stories, and Flows

Vinnstack's AI-First pipeline has several "interrogate/generate" skills scoped to one unit: `interrogate-business` and `interrogate-technical` each run per-Epic, and `story-to-bdd-scenarios` runs per-Story. The new `interrogate-qa` skill is deliberately different — it reads across the WHOLE current set of Epics, Stories, and Process Flows at once, before any BDD scenarios are authored.

Why the wider scope matters: some QA/QC risks are only visible when comparing across stories, never inside a single one:
- shared `Background:`/step-definition reuse across Features (same auth/setup steps re-invented per Feature instead of shared)
- cross-story data/sequencing dependencies (Story B's scenario needs data that only Story A's flow creates) — a real source of order-dependent, flaky BDD scenarios
- inconsistent tagging/coverage conventions between Epics that were interrogated separately (e.g. business/technical rounds run by different people at different times)

General lesson: when adding a new interrogation/review pass to a pipeline of otherwise per-item skills, ask explicitly whether the new pass's value actually comes from batching across items — if so, its process steps should say so explicitly (e.g. "look ACROSS stories for X"), or it just becomes a redundant copy of the per-item skill.

## Related
[[interrogate-qa skill built standalone due to track CHECK constraint]]

## Related

- [[interrogate-qa skill built standalone due to track CHECK constraint]]

%% ai-graph-start %%

**Related notes:**
- [[interrogate-qa skill built standalone due to track CHECK constraint]]
- [[Technical interrogation questions must pass the 2-minute tech-lead test]]
- [[vinnstack pipeline skills share one slicing model - flipping it cascades]]
- [[Vinnstack vinnstack-data-model.html predates the BDD workspace]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]

**Relations:**
- interrogate-qa — *is cross-cutting across* — Epics
- interrogate-qa — *is cross-cutting across* — Stories
- interrogate-qa — *is cross-cutting across* — Flows
- Vinnstack's AI-First pipeline — *has skill* — interrogate-business
- Vinnstack's AI-First pipeline — *has skill* — interrogate-technical
- Vinnstack's AI-First pipeline — *has skill* — story-to-bdd-scenarios
- interrogate-business — *runs per* — Epic
- interrogate-technical — *runs per* — Epic
- story-to-bdd-scenarios — *runs per* — Story
- interrogate-qa — *reads across* — Epics
- interrogate-qa — *reads across* — Stories
- interrogate-qa — *reads across* — Flows
- interrogate-qa — *reads before* — BDD scenarios authored
- QA/QC risks — *are visible when comparing across* — Stories
- shared Background:/step-definition reuse — *is a type of* — QA/QC risks
- cross-story data/sequencing dependencies — *is a type of* — QA/QC risks
- inconsistent tagging/coverage conventions — *is a type of* — QA/QC risks
- cross-story data/sequencing dependencies — *causes* — order-dependent, flaky BDD scenarios
- interrogate-qa — *is a* — skill
- interrogate-qa — *is a* — interrogation/review pass
- interrogate-qa skill — *built standalone due to* — track CHECK constraint
- interrogation/review pass — *can be* — per-item skills

%% ai-graph-end %%