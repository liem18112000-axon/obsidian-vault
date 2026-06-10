---
title: "Regex allowlists of model names go stale when vendors ship new names"
created: 2026-06-10
type: lesson
status: seedling
source: "session 2026-06-10"
tags: [regex, claude-code, hooks, gotcha, git]
---

# Regex allowlists of model names go stale when vendors ship new names

A regex that enumerates product or model names (e.g. `Claude (Opus|Sonnet|Haiku)`) silently stops matching the moment the vendor ships a new name — the `block-coauthor.ps1` git hook missed the "Claude Fable 5" signature for exactly this reason.

Mitigations:

- Pair the name-allowlist with **vendor-invariant anchors** that survive renames — e.g. the `noreply@anthropic.com` email domain and the `Co-Authored-By` trailer label. In the hook these alternatives still caught the full trailer; only a *bare* model-name signature slipped through.
- Treat the name list as a maintenance item: review it whenever a new model/product appears.
- Where false positives are tolerable, prefer a generic shape (`Claude\s+\w+\s*[\d.]*`) over an explicit family list.

Fixed 2026-06-10 by adding `Fable` to the alternation in `~/.claude/hooks/git/block-coauthor.ps1` and verifying with three payloads (full trailer, bare signature, clean commit).

## Related

- [[Claude Code hooks]]
