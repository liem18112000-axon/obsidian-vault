---
ai_hash: 7920e66b76f82ee4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- vinnstack pipeline skills
- prd-to-story
- story-to-process-flow
- implement-story
- story-slicing model
- vertical tracer-bullets
- horizontal layer-by-layer slices
- schema
- logic
- UI
- core-principle change
- skill pack
- old model's vocabulary
- process steps
- pitfalls
- verification sections
- frontmatter descriptions
- versions
- AFK/HITL gate
- contract frozen
- contract stories
- prd-to-story v2.0.0
- prd-to-story v2
- Horizontal story slicing is only workable with contract-first and an integration
  story
- Horizontal slicing sharpens AFK vs HITL classification
- vertical slice
- thin end-to-end path
- Technique
- grepping
- sweeping
- bumping versions
source: session 2026-07-22, horizontal-slice v2 sweep
status: seedling
tags:
- vinnstack
- skill-design
- pipeline
- gotcha
title: vinnstack pipeline skills share one slicing model - flipping it cascades
type: lesson
---

# vinnstack pipeline skills share one slicing model - flipping it cascades

The vinnstack pipeline skills (prd-to-story → story-to-process-flow → implement-story) all encode the SAME story-slicing model in their prose — how a story is scoped, what "done" means, what the plan covers. Changing the model in one skill silently invalidates the others: when prd-to-story v2.0.0 flipped from vertical tracer-bullets to horizontal layer-by-layer slices, implement-story still said "implement the vertical slice end-to-end (schema → logic → UI)" and story-to-process-flow still demanded "a thin end-to-end path, matching the Story's vertical slice".

**Technique:** after any core-principle change in one pipeline skill, grep the whole skill pack for the old model's vocabulary (`vertical|tracer|end-to-end` here) and sweep every hit — process steps, pitfalls, verification sections, AND frontmatter descriptions. Bump versions in the cascade too (major bump where the principle itself flipped).

The AFK/HITL gate is part of the shared model as well: implement-story now has to check "is the contract frozen?" before building, a precondition that only exists because prd-to-story v2 introduced contract stories.

See [[Horizontal story slicing is only workable with contract-first and an integration story]] and [[Horizontal slicing sharpens AFK vs HITL classification]].

## Related

- [[Horizontal story slicing is only workable with contract-first and an integration story]]
- [[Horizontal slicing sharpens AFK vs HITL classification]]

%% ai-graph-start %%

**Related notes:**
- [[Horizontal slicing sharpens AFK vs HITL classification]]
- [[Horizontal contract-first slicing - contract story, parallel layer stories, integration story]]
- [[Horizontal story slicing is only workable with contract-first and an integration story]]
- [[interrogate-qa is cross-cutting across all Epics, Stories, and Flows]]
- [[Vinnstack vinnstack-data-model.html predates the BDD workspace]]

**Relations:**
- vinnstack pipeline skills — *include* — prd-to-story
- vinnstack pipeline skills — *include* — story-to-process-flow
- vinnstack pipeline skills — *include* — implement-story
- vinnstack pipeline skills — *share* — story-slicing model
- prd-to-story — *encodes* — story-slicing model
- story-to-process-flow — *encodes* — story-slicing model
- implement-story — *encodes* — story-slicing model
- story-slicing model — *change in one skill invalidates* — vinnstack pipeline skills
- prd-to-story v2.0.0 — *flipped from* — vertical tracer-bullets
- prd-to-story v2.0.0 — *flipped to* — horizontal layer-by-layer slices
- implement-story — *referred to* — vertical slice
- vertical slice — *includes* — schema
- vertical slice — *includes* — logic
- vertical slice — *includes* — UI
- story-to-process-flow — *referred to* — thin end-to-end path
- thin end-to-end path — *matches* — vertical slice
- Technique — *addresses* — core-principle change
- Technique — *involves* — grepping
- grepping — *targets* — skill pack
- grepping — *for* — old model's vocabulary
- Technique — *involves* — sweeping
- sweeping — *targets* — process steps
- sweeping — *targets* — pitfalls
- sweeping — *targets* — verification sections
- sweeping — *targets* — frontmatter descriptions
- Technique — *involves* — bumping versions
- AFK/HITL gate — *is part of* — story-slicing model
- implement-story — *checks* — contract frozen
- contract frozen — *is a precondition for* — implement-story
- contract frozen — *exists because* — prd-to-story v2 introduced contract stories
- prd-to-story v2 — *introduced* — contract stories
- Horizontal story slicing is only workable with contract-first and an integration story — *related to* — Horizontal slicing sharpens AFK vs HITL classification
- Horizontal story slicing is only workable with contract-first and an integration story — *discusses* — story-slicing model
- Horizontal slicing sharpens AFK vs HITL classification — *discusses* — AFK/HITL gate

%% ai-graph-end %%