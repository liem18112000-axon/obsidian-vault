---
title: "Moderation taxonomies split violence into subtypes"
created: 2026-07-20
type: concept
status: seedling
source: "web research session 2026-07-20"
tags: [content-moderation, taxonomy, llm-safety]
---

# Moderation taxonomies split violence into subtypes

Serious moderation systems never treat "violence" as one label — they split it into subtypes because the right action differs per subtype. OpenAI moderation API distinguishes `violence` (glorification/support of violent acts, threats) from `violence/graphic` (explicit gore/injury detail); it also has harassment/threatening. Llama Guard 3 follows the MLCommons 13-hazard taxonomy where S1 = violent crimes (terrorism, murder, assault, kidnapping, violence toward animals). Perspective API exposes a THREAT attribute.

When building a detector, mirror this: report per-subtype scores (e.g. general violence vs graphic violence vs direct threat) so callers can apply different thresholds/policies per subtype.

## Related

- [[3 Resources/AI/Safety/Violence detection needs a trained classifier, not keyword lists]]
