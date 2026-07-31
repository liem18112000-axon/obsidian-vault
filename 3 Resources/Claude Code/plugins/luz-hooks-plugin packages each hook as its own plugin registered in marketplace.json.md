---
title: "luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json"
created: 2026-06-19
type: howto
status: seedling
source: "session 2026-06-19"
tags: [claude-code, plugins, hooks, luz, kepler]
---

# luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json

The `luz-hooks-plugin` repo (C:\Users\dvtliem\Kepler\luz-hooks-plugin) packages **each hook as its own sub-plugin** under `plugins/<hook-name>/`, containing:

- `.claude-plugin/plugin.json` — the hook's own manifest (name, version, description, keywords).
- `hooks/hooks.json` — the event wiring; references the script via `${CLAUDE_PLUGIN_ROOT}/hooks/<script>.ps1` (use this var, NOT an absolute path, so it resolves wherever the plugin installs).
- `hooks/<script>.ps1` — the hook script itself.

Gotcha (differs from [[luz-skills-plugin packages skills by category directory listed in plugin.json]]): a new hook must **also** be appended to the top-level `.claude-plugin/marketplace.json` `plugins[]` array with `name` + `source: ./plugins/<hook-name>` + description — it is NOT auto-discovered. Hook scripts mirror the local `~/.claude/hooks/<area>/` copy, and the local `~/.claude/settings.json` wires the same script directly via absolute path (the settings.json entry and the plugin's hooks.json are two separate registrations of the same script).

## Related

- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]
