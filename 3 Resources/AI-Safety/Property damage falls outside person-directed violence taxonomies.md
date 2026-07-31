---
title: "Property damage falls outside person-directed violence taxonomies"
created: 2026-07-20
type: lesson
status: seedling
source: "session 2026-07-20"
tags: [content-moderation, taxonomy, llm-judge, gotcha]
---

# Property damage falls outside person-directed violence taxonomies

When building a violence detector, decide up front whether property destruction counts as "violent". Standard moderation taxonomies (OpenAI violence, MLCommons S1) define violence as directed at a **person or group** — so "slit someone's tires" or vandalism is classified NOT violent by a faithful judge, even though it is actionable wrongdoing.

This surfaced as the single disagreement in a 20-case Claude-judge test (C:\Users\dvtliem\AI\ai-test\test_results.md, case #12): the judge returned isViolent=false for tire-slashing, reasoning it targets property not a person. That is defensible, not a bug. Fix by either (a) adding a property-destruction clause to the taxonomy if your policy needs it, or (b) relabeling the ground truth.

Lesson: label ambiguity in a test set is often a taxonomy-definition gap, not a model error — pin the boundary in the prompt/spec before measuring accuracy.

## Related

- [[Moderation taxonomies split violence into subtypes]]
- [[Violence detection needs a trained classifier]]
- [[not keyword lists]]
