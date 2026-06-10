---
title: "Luz plugin repos: how skills and hooks are packaged for distribution"
created: 2026-06-10
type: howto
status: seedling
source: "session 2026-06-10"
tags: [claude-code, plugin, marketplace, skills, hooks]
---

# Luz plugin repos: how skills and hooks are packaged for distribution

Personal skills/hooks under `~/.claude/` are distributed to the team via two Claude Code plugin repos in `C:\Users\dvtliem\Kepler\` (both on `main`):

**`luz-skills-plugin`** — one plugin, many skills. Layout: `skills/<category>/<skill-name>/` (categories: luz-skill, google-skill, claude, earchive, obsidian, …). Root `.claude-plugin/plugin.json` lists only the **category dirs** in its `skills` array, so dropping a new skill folder into an existing category needs **no manifest change**. Exclude machine-local files when copying (e.g. `luz-kubernetes-root.config` cache).

**`luz-hooks-plugin`** — one plugin **per hook bundle**. Layout: `plugins/<plugin-name>/` containing `.claude-plugin/plugin.json` (name/version/long description) and `hooks/hooks.json` (the hook wiring, referencing scripts via `${CLAUDE_PLUGIN_ROOT}/hooks/<script>.ps1`) plus the script(s). New plugins **must** be registered in the root `.claude-plugin/marketplace.json` `plugins` array (name/source/description).

Convention notes: ps1 hook scripts are Windows PowerShell 5.1, pure-ASCII; commit only the new plugin paths (both repos usually carry unrelated WIP).

Applied for [[luz-kubernetes-add-env skill propagates env properties across overlay environments]] and [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]].

## Related

- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
