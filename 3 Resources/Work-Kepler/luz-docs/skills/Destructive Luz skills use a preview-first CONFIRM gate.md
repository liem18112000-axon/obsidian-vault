---
ai_hash: 261ccab5d294091f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-11
entities: []
source: session 2026-06-11 (creating earchive-data-clean)
status: seedling
tags:
- luz
- skills
- convention
- safety
title: Destructive Luz skills use a preview-first CONFIRM gate
type: lesson
---

# Destructive Luz skills use a preview-first CONFIRM gate

Skills whose whole purpose is destructive (data deletion, multi-env config edits) follow a preview-first gate: a plain run only *shows* what would change, and nothing mutates until the run is repeated with an explicit confirmation env var.

Instances of the pattern:

- `earchive-data-clean` (created 2026-06-11, `~/.claude/skills/earchive-data-clean`): without `CONFIRM=1` it connects, resolves target collections (`documents,folders` by default, `ALL=1` for every non-system collection), and prints per-collection counts only. With `CONFIRM=1` it deletes.
- `luz-kubernetes-add-env`: prints unified diffs per environment; writes nothing until re-run with `APPLY=1`.

Two supporting choices: deletion is `deleteMany({})` rather than `drop()`, so indexes and collection options survive (the materialise indexes do not need re-creating); and the agent protocol is to show the user the preview output and only re-run with the confirm flag after explicit user approval.

## Related

- [[3 Resources/Work-Kepler/luz-docs/skills/eArchive dev skills are self-contained copies, not shared helpers]]

%% ai-graph-start %%

**Related notes:**
- [[eArchive dev skills are self-contained copies, not shared helpers]]
- [[deleteMany over kubectl port-forward runs about 5k docs per second]]
- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
- [[Evicting gate campaign-check keys from luz-cache clears L2 only]]

%% ai-graph-end %%