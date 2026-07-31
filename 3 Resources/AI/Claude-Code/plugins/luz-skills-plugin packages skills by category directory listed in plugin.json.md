---
ai_hash: d0fdc179cecca918
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- claude-code
- plugins
- skills
- luz
- kepler
title: luz-skills-plugin packages skills by category directory listed in plugin.json
type: howto
---

# luz-skills-plugin packages skills by category directory listed in plugin.json

The `luz-skills-plugin` repo (C:\Users\dvtliem\Kepler\luz-skills-plugin) groups skills under `skills/<category>/<skill-name>/`, where each skill folder holds its own `SKILL.md` plus any scripts (e.g. `skills/google-skill/google-skill-gke-monitor/`).

Key gotcha: `.claude-plugin/plugin.json` lists the **category directories** (e.g. `"./skills/google-skill/"`), NOT individual skills. So adding a new skill = drop a new subfolder into an already-listed category dir; the plugin loader scans it automatically and **no manifest edit is needed**. `plugin.json` and `marketplace.json` both live in `.claude-plugin/`.

Skill files are mirrored verbatim from the local `~/.claude/skills/<skill>/` copy (path refs like `~/.claude/skills/...` are kept as-is). Contrast with [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]], where every new hook DOES require a manifest edit.

## Related

- [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]]

%% ai-graph-start %%

**Related notes:**
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]]
- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
- [[luz_kubernetes overlay layout system.properties per env-envservice]]

%% ai-graph-end %%