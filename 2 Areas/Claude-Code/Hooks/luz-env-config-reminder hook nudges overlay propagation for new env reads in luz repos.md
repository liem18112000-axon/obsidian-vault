---
title: "luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos"
created: 2026-06-10
type: howto
status: seedling
source: "session 2026-06-10"
tags: [claude-code, hook, luz, env-config]
---

# luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos

PostToolUse hook `~/.claude/hooks/luz/luz-env-config-reminder.ps1` (registered in `settings.json` under the `Edit|Write|MultiEdit` matcher) watches edits inside any repo whose path contains a `luz-`/`luz_` segment (luz_docs, luz-store, luz-enrichment, …).

When the added text introduces an env read or env config — `System.getenv("X")`, `@ConfigProperty(name="X")`, MicroProfile `.getValue("X")`/`.getOptionalValue("X")`, or new `KEY=`/`KEY:` lines in `.properties`/`.env`/docker-compose/configmap files — it injects additionalContext naming the detected variables and telling Claude to offer the [[luz-kubernetes-add-env skill propagates env properties across overlay environments]] so the var also lands in the 7 overlay environments.

Deliberately silent inside `luz_kubernetes` itself / `kubernetes-overlays` paths (that's the skill's target, not a source) and in `.git`, `node_modules`, `target`, `build`. It normalizes the repo segment to a dashed overlay name for the SERVICE hint (`luz_docs` → `luz-docs`).

Only var-shaped names fire: `[A-Z][A-Z0-9_]{2,}` — lowercase config keys won't trigger it.

## Related

- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[2 Areas/Kepler/luz_kubernetes overlay layout system.properties per env-envservice]]
