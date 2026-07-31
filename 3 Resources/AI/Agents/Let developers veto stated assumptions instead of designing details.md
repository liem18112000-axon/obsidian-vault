---
ai_hash: d9b080e9e0dff506
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-21
entities: []
source: session 2026-07-21, LUZ-156281 developer feedback
status: seedling
tags:
- ai-workflow
- review
- prompt-design
- vinnstack
title: Let developers veto stated assumptions instead of designing details
type: model
---

# Let developers veto stated assumptions instead of designing details

When an AI drafts a technical design for human review, every sub-decision the AI resolved on its own should surface as an explicit "Assumptions / defaults chosen" list — not as questions. Reviewers then *veto* a wrong default (a cheap recognition task) instead of *designing* the detail from scratch (an expensive generation task). Nothing is silently decided, yet the human-facing question count stays small and high-level.

Introduced in vinnstack `interrogate-technical` v2 after developers reported detail-level questions were unanswerable; pairs with the altitude rule in [[Technical interrogation questions must pass the 2-minute tech-lead test]].

## Related

- [[Technical interrogation questions must pass the 2-minute tech-lead test]]

%% ai-graph-start %%

**Related notes:**
- [[Technical interrogation questions must pass the 2-minute tech-lead test]]
- [[Find then adversarial-refute verify pass cuts AI reviewer false positives]]
- [[Keep the release gate deterministic; put AI judgment in upstream signals not the gate]]
- [[AI as an accelerator with a human review gate]]
- [[interrogate-qa skill built standalone due to track CHECK constraint]]

%% ai-graph-end %%