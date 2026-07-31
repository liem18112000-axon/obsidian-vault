---
title: "Horizontal slicing sharpens AFK vs HITL classification"
created: 2026-07-22
type: argument
status: seedling
source: "session 2026-07-22, prd-to-story v2.0.0"
tags: [vinnstack, afk-hitl, horizontal-slicing, skill-design]
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
