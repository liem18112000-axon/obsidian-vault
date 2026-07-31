---
ai_hash: 718adac53cd55e8f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-22
entities:
- Horizontal slicing
- AFK vs HITL classification
- AFK
- HITL
- story type
- structural boundary
- Contract stories
- shared interfaces
- API
- schema
- message shape
- other layers
- services
- shared-contract criterion
- Vertical slicing
- Integration stories
- human eyes
- cross-layer wiring
- end-to-end verification
- Layer-implementation stories
- FROZEN contract
- AFK candidates
- checklist
- bounded blast radius
- security change
- PII change
- PRD fully specifies the approach
- test seam at the layer boundary
- contract tests
- unit tests
- stubs
- tracer-bullet story
- contract-ish work
- mechanical work
- AFK eligibility
- risky decisions
- middle-layer implementations
- autonomous-loop candidates
- vinnstack prd-to-story skill v2.0.0
- Decision
- Horizontal story slicing
- contract-first
- integration story
source: session 2026-07-22, prd-to-story v2.0.0
status: seedling
tags:
- vinnstack
- afk-hitl
- horizontal-slicing
- skill-design
title: Horizontal slicing sharpens AFK vs HITL classification
type: argument
---

# Horizontal slicing sharpens AFK vs HITL classification

When stories are sliced horizontally (one layer of one module per story), the AFK/HITL boundary becomes structural instead of judgment-heavy — the story type itself predicts the class.

- **Contract stories are always HITL:** they define shared interfaces (API / schema / message shape) other layers and services depend on — exactly the shared-contract criterion that already forced HITL under vertical slicing.
- **Integration stories are HITL:** cross-layer wiring and end-to-end verification need human eyes.
- **Only layer-implementation stories behind a FROZEN contract are AFK candidates**, and still must pass the checklist: bounded blast radius (~≤3 files in one layer of one module), no security/PII change, PRD fully specifies the approach, and a test seam exists at the layer boundary (contract/unit tests against stubs).

Under vertical slicing every tracer-bullet story mixed contract-ish and mechanical work, so AFK eligibility had to be argued per story. Horizontal slicing concentrates the risky decisions into the contract + integration stories and leaves the middle-layer implementations as clean autonomous-loop candidates.

Decision made in vinnstack prd-to-story skill v2.0.0. When in doubt → HITL still applies.

## Related

- [[Horizontal story slicing is only workable with contract-first and an integration story]]

%% ai-graph-start %%

**Related notes:**
- [[Horizontal story slicing is only workable with contract-first and an integration story]]
- [[Horizontal contract-first slicing - contract story, parallel layer stories, integration story]]
- [[vinnstack pipeline skills share one slicing model - flipping it cascades]]

**Relations:**
- Horizontal slicing — *sharpens* — AFK vs HITL classification
- Horizontal slicing — *makes boundary* — structural boundary
- story type — *predicts* — AFK vs HITL classification
- Contract stories — *are always* — HITL
- Contract stories — *define* — shared interfaces
- shared interfaces — *include* — API
- shared interfaces — *include* — schema
- shared interfaces — *include* — message shape
- other layers — *depend on* — shared interfaces
- services — *depend on* — shared interfaces
- shared-contract criterion — *forced* — HITL
- shared-contract criterion — *under* — Vertical slicing
- Integration stories — *are* — HITL
- Integration stories — *need* — human eyes
- human eyes — *for* — cross-layer wiring
- human eyes — *for* — end-to-end verification
- Layer-implementation stories — *are AFK candidates behind* — FROZEN contract
- AFK candidates — *must pass* — checklist
- checklist — *includes* — bounded blast radius
- checklist — *includes* — no security change
- checklist — *includes* — no PII change
- checklist — *includes* — PRD fully specifies the approach
- checklist — *includes* — test seam at the layer boundary
- test seam at the layer boundary — *uses* — contract tests
- test seam at the layer boundary — *uses* — unit tests
- test seam at the layer boundary — *against* — stubs
- Vertical slicing — *mixed* — contract-ish work
- Vertical slicing — *mixed* — mechanical work
- contract-ish work — *and mechanical work in* — every tracer-bullet story
- AFK eligibility — *had to be argued per story under* — Vertical slicing
- Horizontal slicing — *concentrates* — risky decisions
- risky decisions — *into* — Contract stories
- risky decisions — *into* — Integration stories
- Horizontal slicing — *leaves* — middle-layer implementations
- middle-layer implementations — *as* — autonomous-loop candidates
- Decision — *made in* — vinnstack prd-to-story skill v2.0.0
- Horizontal story slicing — *is workable with* — contract-first
- Horizontal story slicing — *is workable with* — an integration story

%% ai-graph-end %%