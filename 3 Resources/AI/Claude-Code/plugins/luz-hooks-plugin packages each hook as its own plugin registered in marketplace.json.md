---
ai_hash: b3bb9361d6a56acb
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-19
entities: []
source: session 2026-06-19
status: seedling
tags:
- claude-code
- plugins
- hooks
- luz
- kepler
title: luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json
type: howto
---

# luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json

The `luz-hooks-plugin` repo (C:\Users\dvtliem\Kepler\luz-hooks-plugin) packages **each hook as its own sub-plugin** under `plugins/<hook-name>/`, containing:

- `.claude-plugin/plugin.json` — the hook's own manifest (name, version, description, keywords).
- `hooks/hooks.json` — the event wiring; references the script via `${CLAUDE_PLUGIN_ROOT}/hooks/<script>.ps1` (use this var, NOT an absolute path, so it resolves wherever the plugin installs).
- `hooks/<script>.ps1` — the hook script itself.

Gotcha (differs from [[luz-skills-plugin packages skills by category directory listed in plugin.json]]): a new hook must **also** be appended to the top-level `.claude-plugin/marketplace.json` `plugins[]` array with `name` + `source: ./plugins/<hook-name>` + description — it is NOT auto-discovered. Hook scripts mirror the local `~/.claude/hooks/<area>/` copy, and the local `~/.claude/settings.json` wires the same script directly via absolute path (the settings.json entry and the plugin's hooks.json are two separate registrations of the same script).

## Related

- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]

%% ai-graph-start %%

**Related notes:**
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]
- [[luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos]]
- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[Relocating a hardcoded-path hook integration self-locate or patch every reference site]]

%% ai-graph-end %%