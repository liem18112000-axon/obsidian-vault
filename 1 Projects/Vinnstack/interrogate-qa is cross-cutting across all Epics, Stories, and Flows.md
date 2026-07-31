---
title: "interrogate-qa is cross-cutting across all Epics, Stories, and Flows"
created: 2026-07-11
type: concept
status: seedling
source: "session 2026-07-11 — creating interrogate-qa skill"
tags: [vinnstack, ai-first-pipeline, test-design, skill-design]
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
