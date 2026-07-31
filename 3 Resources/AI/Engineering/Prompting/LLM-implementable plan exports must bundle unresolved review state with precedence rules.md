---
title: "LLM-implementable plan exports must bundle unresolved review state with precedence rules"
created: 2026-07-04
type: model
status: seedling
source: "session 2026-07-04, vinnstack flow exporter"
tags: [llm, handoff, export, vinnstack]
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
