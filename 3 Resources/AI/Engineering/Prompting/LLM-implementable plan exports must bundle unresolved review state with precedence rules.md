---
ai_hash: a8cc7f2028d762bf
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack flow exporter
status: seedling
tags:
- llm
- handoff
- export
- vinnstack
title: LLM-implementable plan exports must bundle unresolved review state with precedence
  rules
type: model
---

# LLM-implementable plan exports must bundle unresolved review state with precedence rules

When exporting a generated plan (process flow, PRD, design doc) as a file for an implementing agent to act on, the plan text alone is NOT the handoff - the export must be self-contained:

- **Unresolved review comments**, each anchored to its exact quoted passage, with attachment paths the agent can Read.
- **An explicit precedence rule** in the preamble: "where an outstanding comment conflicts with the plan, the comment wins" - otherwise the agent implements the passage the reviewer already rejected.
- Binding context the plan assumes: target repos, parent artifact status (PRD approved?), version + generation provenance.
- Quality signals (e.g. the AI self-assessment) marked stale when they rated an older version.

Write the export where agents already have access (the vault exposed via --add-dir) and let the content repo version it - the file doubles as a durable audit of what was handed off. Reuse the same comment-block formatter as the regeneration prompt so the two consumers can't drift.

## Related

- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]

%% ai-graph-start %%

**Related notes:**
- [[Inject anchored inline comments into LLM regeneration prompts as quoted passages]]
- [[AI self-critique loop - a post-generation critic pass rates the artifact and feeds the next run]]
- [[Version-stamp quality ratings so stale feedback stops driving regeneration]]
- [[PRD-parity checklist - what comment-driven regenerate with versions actually requires]]
- [[TextQuoteSelector anchoring survives document regeneration]]

%% ai-graph-end %%