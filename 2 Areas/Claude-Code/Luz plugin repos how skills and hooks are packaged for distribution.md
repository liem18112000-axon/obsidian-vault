---
ai_hash: de85a0e0a6fa50b2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- Luz plugin repos
- skills
- hooks
- distribution
- Claude Code plugin repos
- '`~/.claude/`'
- '`C:\Users\dvtliem\Kepler\`'
- '`luz-skills-plugin`'
- '`luz-hooks-plugin`'
- '`main` branch'
- skill categories
- '`luz-skill`'
- '`google-skill`'
- '`claude` (category)'
- '`earchive`'
- '`obsidian`'
- '`.claude-plugin/plugin.json`'
- '`skills` array'
- '`luz-kubernetes-root.config`'
- '`hooks/hooks.json`'
- '`marketplace.json`'
- '`plugins` array'
- ps1 hook scripts
- Windows PowerShell 5.1
- pure-ASCII
- '`luz-kubernetes-add-env skill propagates env properties across overlay environments`'
- '`luz-env-config-reminder hook nudges overlay propagation for new env reads in luz
  repos`'
source: session 2026-06-10
status: seedling
tags:
- claude-code
- plugin
- marketplace
- skills
- hooks
title: 'Luz plugin repos: how skills and hooks are packaged for distribution'
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]
- [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]]
- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
- [[luz_kubernetes overlay layout system.properties per env-envservice]]

**Relations:**
- Luz plugin repos — *package* — skills
- Luz plugin repos — *package* — hooks
- Luz plugin repos — *enable* — distribution
- skills — *are located under* — `~/.claude/`
- hooks — *are located under* — `~/.claude/`
- skills — *are distributed via* — Claude Code plugin repos
- hooks — *are distributed via* — Claude Code plugin repos
- Claude Code plugin repos — *are located at* — `C:\Users\dvtliem\Kepler\`
- Claude Code plugin repos — *include* — `luz-skills-plugin`
- Claude Code plugin repos — *include* — `luz-hooks-plugin`
- `luz-skills-plugin` — *manages* — skills
- `luz-hooks-plugin` — *manages* — hooks
- `luz-skills-plugin` — *uses branch* — `main` branch
- `luz-hooks-plugin` — *uses branch* — `main` branch
- `luz-skills-plugin` — *has layout* — `skills/<category>/<skill-name>/`
- `luz-skills-plugin` — *uses manifest* — `.claude-plugin/plugin.json`
- `.claude-plugin/plugin.json` — *lists* — skill categories
- skill categories — *are listed in* — `skills` array
- skill categories — *include* — `luz-skill`
- skill categories — *include* — `google-skill`
- skill categories — *include* — `claude` (category)
- skill categories — *include* — `earchive`
- skill categories — *include* — `obsidian`
- `luz-skills-plugin` — *excludes* — `luz-kubernetes-root.config`
- `luz-hooks-plugin` — *has layout* — `plugins/<plugin-name>/`
- `plugins/<plugin-name>/` — *contains* — `.claude-plugin/plugin.json`
- `plugins/<plugin-name>/` — *contains* — `hooks/hooks.json`
- `hooks/hooks.json` — *references scripts via* — `${CLAUDE_PLUGIN_ROOT}/hooks/<script>.ps1`
- new plugins — *must be registered in* — `marketplace.json`
- `marketplace.json` — *contains* — `plugins` array
- ps1 hook scripts — *are written in* — Windows PowerShell 5.1
- ps1 hook scripts — *are* — pure-ASCII
- `luz-kubernetes-add-env skill propagates env properties across overlay environments` — *is a type of* — skills
- `luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos` — *is a type of* — hooks
- `luz-kubernetes-add-env skill propagates env properties across overlay environments` — *is an example for* — Luz plugin repos
- `luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos` — *is an example for* — Luz plugin repos

%% ai-graph-end %%