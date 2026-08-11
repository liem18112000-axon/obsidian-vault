---
ai_hash: 04316c7fc127affe
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-24
entities: []
source: LUZ-157476 session 2026-07-24
status: seedling
tags:
- communication
- taxonomy
- vendor
- design-decision
title: Unbounded provider prose justifies fallback-first mapper plus vendor list request
type: lesson
---

# Unbounded provider prose justifies fallback-first mapper plus vendor list request

Stakeholder framing that worked for LUZ-157476: when a mapping must be built on an external provider's free-text messages (Payrexx), acknowledge the input set is unbounded (no published catalog, possibly non-English), then split the plan into: LONG-TERM — formally ask the vendor for the complete message list (contract-based mapping); SHORT-TERM — build the mapper from all patterns observed in PROD to date, with a generic 'other' fallback + raw-message logging so unknown patterns never break the flow and each occurrence feeds the mapping table.

The two sentences that pre-empt management pushback: 'anything unrecognized falls back to a safe generic category and is logged' (answers: what if a new pattern appears?) and 'we verified the public docs only describe backoffice codes, not API strings' (answers: did you check their documentation?).

## Related
- [[Payrexx publishes no catalog of API wrapper error messages]]
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]

%% ai-graph-start %%

**Related notes:**
- [[LUZ-157476 maps failure categories in luz_store only, overriding the boundary recommendation]]
- [[Observed Payrexx prose vocabulary in dev is only three messages]]
- [[LUZ-157476 proposes seven failure categories with per-channel customer copy]]
- [[Persist payment failure category at charge time, never derive from prose]]
- [[LUZ-157476 decline taxonomy maps codes at luz_online_payment boundary]]

%% ai-graph-end %%