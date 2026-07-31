---
title: "Unbounded provider prose justifies fallback-first mapper plus vendor list request"
created: 2026-07-24
type: lesson
status: seedling
source: "LUZ-157476 session 2026-07-24"
tags: [communication, taxonomy, vendor, design-decision]
---

# Unbounded provider prose justifies fallback-first mapper plus vendor list request

Stakeholder framing that worked for LUZ-157476: when a mapping must be built on an external provider's free-text messages (Payrexx), acknowledge the input set is unbounded (no published catalog, possibly non-English), then split the plan into: LONG-TERM — formally ask the vendor for the complete message list (contract-based mapping); SHORT-TERM — build the mapper from all patterns observed in PROD to date, with a generic 'other' fallback + raw-message logging so unknown patterns never break the flow and each occurrence feeds the mapping table.

The two sentences that pre-empt management pushback: 'anything unrecognized falls back to a safe generic category and is logged' (answers: what if a new pattern appears?) and 'we verified the public docs only describe backoffice codes, not API strings' (answers: did you check their documentation?).

## Related
- [[Payrexx publishes no catalog of API wrapper error messages]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
