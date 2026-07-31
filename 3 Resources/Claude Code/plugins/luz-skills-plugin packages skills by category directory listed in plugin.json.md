---
title: "luz-skills-plugin packages skills by category directory listed in plugin.json"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [claude-code, plugins, skills, luz, kepler]
---

# luz-skills-plugin packages skills by category directory listed in plugin.json

The `luz-skills-plugin` repo (C:\Users\dvtliem\Kepler\luz-skills-plugin) groups skills under `skills/<category>/<skill-name>/`, where each skill folder holds its own `SKILL.md` plus any scripts (e.g. `skills/google-skill/google-skill-gke-monitor/`).

Key gotcha: `.claude-plugin/plugin.json` lists the **category directories** (e.g. `"./skills/google-skill/"`), NOT individual skills. So adding a new skill = drop a new subfolder into an already-listed category dir; the plugin loader scans it automatically and **no manifest edit is needed**. `plugin.json` and `marketplace.json` both live in `.claude-plugin/`.

Skill files are mirrored verbatim from the local `~/.claude/skills/<skill>/` copy (path refs like `~/.claude/skills/...` are kept as-is). Contrast with [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]], where every new hook DOES require a manifest edit.

## Related

- [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]]
