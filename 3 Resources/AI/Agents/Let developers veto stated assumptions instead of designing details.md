---
title: "Let developers veto stated assumptions instead of designing details"
created: 2026-07-21
type: model
status: seedling
source: "session 2026-07-21, LUZ-156281 developer feedback"
tags: [ai-workflow, review, prompt-design, vinnstack]
---

# Let developers veto stated assumptions instead of designing details

When an AI drafts a technical design for human review, every sub-decision the AI resolved on its own should surface as an explicit "Assumptions / defaults chosen" list — not as questions. Reviewers then *veto* a wrong default (a cheap recognition task) instead of *designing* the detail from scratch (an expensive generation task). Nothing is silently decided, yet the human-facing question count stays small and high-level.

Introduced in vinnstack `interrogate-technical` v2 after developers reported detail-level questions were unanswerable; pairs with the altitude rule in [[Technical interrogation questions must pass the 2-minute tech-lead test]].

## Related

- [[Technical interrogation questions must pass the 2-minute tech-lead test]]
