---
ai_hash: bc554956504d50e7
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities: []
source: session 2026-06-10
status: seedling
tags:
- yaml
- gotcha
- claude-code
- skill
- frontmatter
title: Unquoted YAML frontmatter description breaks on colon-space in SKILL.md
type: lesson
---

# Unquoted YAML frontmatter description breaks on colon-space in SKILL.md

A `SKILL.md` frontmatter `description:` written as an unquoted (plain) YAML scalar breaks with `mapping values are not allowed in this context` the moment the text contains a colon followed by a space (e.g. `preview-first: the script prints...`). YAML reads the inner `: ` as a nested mapping inside a plain scalar, which is illegal.

The trap is asymmetric: **Claude Code's local skill loader tolerates it** (skills under `~/.claude/skills/` keep working), but the **plugin/marketplace loader parses strictly** and rejects the skill — so the bug only surfaces after the skill is published to a plugin repo.

Fix: single-quote the whole description (`description: '...'`), doubling any internal apostrophes (`''`). Single quotes are safer than double here because skill descriptions are full of `"trigger phrases"`.

Prevention: after editing any SKILL.md frontmatter, validate with a quick `yaml.safe_load` sweep over `skills/**/SKILL.md` — a 2026-06-10 sweep found 5 broken files in luz-skills-plugin plus 4 more local-only skills, all from the same `: ` pattern.

Applies to [[2 Areas/Claude-Code/Luz plugin repos how skills and hooks are packaged for distribution]].

## Related

- [[2 Areas/Claude-Code/Luz plugin repos how skills and hooks are packaged for distribution]]

%% ai-graph-start %%

**Related notes:**
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]

%% ai-graph-end %%