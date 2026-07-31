---
title: "Destructive Luz skills use a preview-first CONFIRM gate"
created: 2026-06-11
type: lesson
status: seedling
source: "session 2026-06-11 (creating earchive-data-clean)"
tags: [luz, skills, convention, safety]
---

# Destructive Luz skills use a preview-first CONFIRM gate

Skills whose whole purpose is destructive (data deletion, multi-env config edits) follow a preview-first gate: a plain run only *shows* what would change, and nothing mutates until the run is repeated with an explicit confirmation env var.

Instances of the pattern:

- `earchive-data-clean` (created 2026-06-11, `~/.claude/skills/earchive-data-clean`): without `CONFIRM=1` it connects, resolves target collections (`documents,folders` by default, `ALL=1` for every non-system collection), and prints per-collection counts only. With `CONFIRM=1` it deletes.
- `luz-kubernetes-add-env`: prints unified diffs per environment; writes nothing until re-run with `APPLY=1`.

Two supporting choices: deletion is `deleteMany({})` rather than `drop()`, so indexes and collection options survive (the materialise indexes do not need re-creating); and the agent protocol is to show the user the preview output and only re-run with the confirm flag after explicit user approval.

## Related

- [[3 Resources/Work-Kepler/luz-docs/skills/eArchive dev skills are self-contained copies, not shared helpers]]
