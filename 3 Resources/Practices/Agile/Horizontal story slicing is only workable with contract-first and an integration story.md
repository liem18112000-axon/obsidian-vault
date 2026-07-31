---
title: "Horizontal story slicing is only workable with contract-first and an integration story"
created: 2026-07-22
type: howto
status: seedling
source: "session 2026-07-22, prd-to-story v2 research"
tags: [agile, story-splitting, horizontal-slicing, contract-first, vinnstack]
---

# Horizontal story slicing is only workable with contract-first and an integration story

Horizontal slicing (one story per architectural layer per module, instead of end-to-end vertical slices) fails by default because no layer story is independently demoable — it only becomes workable when the decomposition adds two mandatory story types and three discipline rules.

**The two mandatory story types:**
- **Contract story (filed first):** freeze the interface between layers before any implementation — API spec, DB schema, event/message shape. Generate stubs/mocks from the frozen contract so dependent layer stories proceed in parallel instead of serially blocking.
- **Integration story (filed last, per feature):** wire the layers together and verify the end-to-end path. This is the only place a demo can happen, so skipping or merging it is where horizontal slicing silently fails.

**The discipline rules:**
- Every layer story gets testable acceptance at its own boundary (unit tests + contract tests against the stub) — otherwise it is unverifiable work, not a story.
- Dependency order is contracts → data → logic → API → UI → integration.
- Schedule the layer the team dislikes (usually UI) deliberately; left to default it drifts to last and quality suffers.

A contract change after dependent stories are filed reopens all of them — surface it, never patch silently.

Applied in vinnstack prd-to-story skill v2.0.0 (switched from vertical tracer-bullet slices to horizontal).

## Related

- [[Horizontal slicing sharpens AFK vs HITL classification]]
