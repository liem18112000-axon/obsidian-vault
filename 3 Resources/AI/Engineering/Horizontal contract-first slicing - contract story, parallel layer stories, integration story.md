---
title: "Horizontal contract-first slicing - contract story, parallel layer stories, integration story"
created: 2026-07-23
type: model
status: seedling
source: "session 2026-07-23"
tags: [slicing, stories, contract-first, vinnstack, methodology]
---

# Horizontal contract-first slicing - contract story, parallel layer stories, integration story

Vinnstack's story-decomposition methodology v2 (prd-to-story 2.0.0, replacing v1's vertical tracer-bullet slices): decompose a PRD top-down into modules × layers (schema/data → domain logic → API → UI), one story per layer per module — plus two MANDATORY story types that make horizontal slicing work:

- **Contract story (first, always HITL):** freeze the interface between layers (API spec / DB schema / event shape) before anything implements against it; generate stubs/mocks FROM the contract so dependent layer stories proceed in parallel.
- **Layer stories (the only AFK candidates):** one layer × one module behind a FROZEN contract, acceptance at their own boundary (unit + contract tests vs the stubs) — explicitly NOT independently demoable.
- **Integration story (last, always HITL):** wire the layers, verify end-to-end — the demo lives here.

Rules with teeth: a contract change after dependents are filed reopens ALL of them (surface loudly, never patch silently); order = contracts → data → logic → API → UI → integration; don't let UI drift to last by default — schedule it deliberately; when in doubt on AFK/HITL → HITL.

Why the pivot matters: vertical slices optimize for demoability per story; horizontal+contract-first optimizes for PARALLELISM (multiple modules/agents building simultaneously against frozen stubs) — the right trade when an autonomous build loop supplies cheap parallel implementers and humans own only contracts + integration.

Related: [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]
