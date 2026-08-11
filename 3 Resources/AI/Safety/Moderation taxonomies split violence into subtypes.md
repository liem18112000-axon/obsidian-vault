---
ai_hash: 0db2b110b896a4c9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-20
entities: []
source: web research session 2026-07-20
status: seedling
tags:
- content-moderation
- taxonomy
- llm-safety
title: Moderation taxonomies split violence into subtypes
type: concept
---

# Moderation taxonomies split violence into subtypes

Serious moderation systems never treat "violence" as one label — they split it into subtypes because the right action differs per subtype. OpenAI moderation API distinguishes `violence` (glorification/support of violent acts, threats) from `violence/graphic` (explicit gore/injury detail); it also has harassment/threatening. Llama Guard 3 follows the MLCommons 13-hazard taxonomy where S1 = violent crimes (terrorism, murder, assault, kidnapping, violence toward animals). Perspective API exposes a THREAT attribute.

When building a detector, mirror this: report per-subtype scores (e.g. general violence vs graphic violence vs direct threat) so callers can apply different thresholds/policies per subtype.

## Related

- [[3 Resources/AI/Safety/Violence detection needs a trained classifier, not keyword lists]]

%% ai-graph-start %%

**Related notes:**
- [[Violence detection needs a trained classifier, not keyword lists]]
- [[Model options for detecting violent text by weight class]]
- [[Property damage falls outside person-directed violence taxonomies]]

%% ai-graph-end %%