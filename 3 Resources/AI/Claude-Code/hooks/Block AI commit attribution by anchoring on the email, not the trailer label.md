---
ai_hash: cd3c353ad6711613
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: session 2026-06-05
status: seedling
tags:
- claude-code
- hooks
- git
- regex
title: Block AI commit attribution by anchoring on the email, not the trailer label
type: howto
---

# Block AI commit attribution by anchoring on the email, not the trailer label

When blocking AI attribution in git commits, anchor the deny pattern on the **email address** (`noreply@anthropic\.com`) rather than only the `Co-Authored-By` label or specific model names. Model names and versions churn (Opus 4.8 → whatever comes next) and attribution can appear without the trailer label at all (e.g. a bare "Generated with Claude Code" line), but the signature email stays constant across all of them.

A robust pattern layers three anchors:

```
(?i)(Co-?Authored-?By|noreply@anthropic\.com|Generated\s+with\s+\[?Claude|Claude\s+(Opus|Sonnet|Haiku)\s*[\d.]*)
```

- the trailer label in any spelling/casing,
- the email (catches any future model/version),
- the "Generated with Claude Code" footer and explicit model-family names as belt-and-braces.

Applied in `~/.claude/hooks/git/block-coauthor.ps1` (2026-06-05).

## Related

- [[Command-scanning git-commit hooks miss flag-separated forms and -F message files]]

%% ai-graph-start %%

**Related notes:**
- [[Command-scanning git-commit hooks miss flag-separated forms and -F message files]]
- [[Regex allowlists of model names go stale when vendors ship new names]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]
- [[Hard-exclude an AI agent from a resource by shrinking its file grant, not by prompting]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]

%% ai-graph-end %%