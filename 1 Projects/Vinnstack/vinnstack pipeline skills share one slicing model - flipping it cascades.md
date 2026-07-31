---
title: "vinnstack pipeline skills share one slicing model - flipping it cascades"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22, horizontal-slice v2 sweep"
tags: [vinnstack, skill-design, pipeline, gotcha]
---

# vinnstack pipeline skills share one slicing model - flipping it cascades

The vinnstack pipeline skills (prd-to-story → story-to-process-flow → implement-story) all encode the SAME story-slicing model in their prose — how a story is scoped, what "done" means, what the plan covers. Changing the model in one skill silently invalidates the others: when prd-to-story v2.0.0 flipped from vertical tracer-bullets to horizontal layer-by-layer slices, implement-story still said "implement the vertical slice end-to-end (schema → logic → UI)" and story-to-process-flow still demanded "a thin end-to-end path, matching the Story's vertical slice".

**Technique:** after any core-principle change in one pipeline skill, grep the whole skill pack for the old model's vocabulary (`vertical|tracer|end-to-end` here) and sweep every hit — process steps, pitfalls, verification sections, AND frontmatter descriptions. Bump versions in the cascade too (major bump where the principle itself flipped).

The AFK/HITL gate is part of the shared model as well: implement-story now has to check "is the contract frozen?" before building, a precondition that only exists because prd-to-story v2 introduced contract stories.

See [[Horizontal story slicing is only workable with contract-first and an integration story]] and [[Horizontal slicing sharpens AFK vs HITL classification]].

## Related

- [[Horizontal story slicing is only workable with contract-first and an integration story]]
- [[Horizontal slicing sharpens AFK vs HITL classification]]
