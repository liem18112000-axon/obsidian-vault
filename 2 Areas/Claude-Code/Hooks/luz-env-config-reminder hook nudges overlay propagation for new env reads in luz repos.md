---
ai_hash: f714f101a9e5640f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-10
entities:
- luz-env-config-reminder hook
- overlay propagation
- luz repos
- ~/.claude/hooks/luz/luz-env-config-reminder.ps1
- settings.json
- Edit|Write|MultiEdit matcher
- Claude
- luz-kubernetes-add-env skill
- overlay environments
- luz_kubernetes
- kubernetes-overlays
- ignored paths
- luz_docs
- luz-docs
- MicroProfile
- System.getenv("X")
- '@ConfigProperty(name="X")'
- env read
- env config
- additionalContext
- SERVICE hint
- var-shaped names
- luz-kubernetes overlay layout system
- .properties files
- .env files
- docker-compose files
- configmap files
source: session 2026-06-10
status: seedling
tags:
- claude-code
- hook
- luz
- env-config
title: luz-env-config-reminder hook nudges overlay propagation for new env reads in
  luz repos
type: howto
---

# luz-env-config-reminder hook nudges overlay propagation for new env reads in luz repos

PostToolUse hook `~/.claude/hooks/luz/luz-env-config-reminder.ps1` (registered in `settings.json` under the `Edit|Write|MultiEdit` matcher) watches edits inside any repo whose path contains a `luz-`/`luz_` segment (luz_docs, luz-store, luz-enrichment, …).

When the added text introduces an env read or env config — `System.getenv("X")`, `@ConfigProperty(name="X")`, MicroProfile `.getValue("X")`/`.getOptionalValue("X")`, or new `KEY=`/`KEY:` lines in `.properties`/`.env`/docker-compose/configmap files — it injects additionalContext naming the detected variables and telling Claude to offer the [[luz-kubernetes-add-env skill propagates env properties across overlay environments]] so the var also lands in the 7 overlay environments.

Deliberately silent inside `luz_kubernetes` itself / `kubernetes-overlays` paths (that's the skill's target, not a source) and in `.git`, `node_modules`, `target`, `build`. It normalizes the repo segment to a dashed overlay name for the SERVICE hint (`luz_docs` → `luz-docs`).

Only var-shaped names fire: `[A-Z][A-Z0-9_]{2,}` — lowercase config keys won't trigger it.

## Related

- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[2 Areas/Kepler/luz_kubernetes overlay layout system.properties per env-envservice]]

%% ai-graph-start %%

**Related notes:**
- [[luz-kubernetes-add-env skill propagates env properties across overlay environments]]
- [[Luz plugin repos how skills and hooks are packaged for distribution]]
- [[luz_kubernetes overlay layout system.properties per env-envservice]]
- [[luz-hooks-plugin packages each hook as its own plugin registered in marketplace.json]]
- [[luz-skills-plugin packages skills by category directory listed in plugin.json]]

**Relations:**
- luz-env-config-reminder hook — *nudges* — overlay propagation
- luz-env-config-reminder hook — *operates on* — luz repos
- luz-env-config-reminder hook — *is implemented by* — ~/.claude/hooks/luz/luz-env-config-reminder.ps1
- ~/.claude/hooks/luz/luz-env-config-reminder.ps1 — *registered in* — settings.json
- settings.json — *uses* — Edit|Write|MultiEdit matcher
- luz-env-config-reminder hook — *monitors edits in* — luz repos
- luz repos — *contain* — luz-/luz_ segment
- luz_docs — *is an example of* — luz repos
- luz-env-config-reminder hook — *detects* — env read
- luz-env-config-reminder hook — *detects* — env config
- env read — *example* — System.getenv("X")
- env config — *example* — @ConfigProperty(name="X")
- env config — *example* — MicroProfile
- env config — *found in* — .properties files
- env config — *found in* — .env files
- env config — *found in* — docker-compose files
- env config — *found in* — configmap files
- luz-env-config-reminder hook — *injects* — additionalContext
- additionalContext — *prompts* — Claude
- Claude — *to offer* — luz-kubernetes-add-env skill
- luz-kubernetes-add-env skill — *propagates to* — overlay environments
- luz-env-config-reminder hook — *is silent in* — luz_kubernetes
- luz-env-config-reminder hook — *is silent in* — kubernetes-overlays
- luz-env-config-reminder hook — *is silent in* — ignored paths
- luz_kubernetes — *is target for* — luz-kubernetes-add-env skill
- kubernetes-overlays — *is target for* — luz-kubernetes-add-env skill
- luz-env-config-reminder hook — *normalizes* — luz_docs
- luz_docs — *normalizes to* — luz-docs
- luz-docs — *is used for* — SERVICE hint
- luz-env-config-reminder hook — *triggers on* — var-shaped names
- luz-kubernetes-add-env skill — *is related to* — luz-env-config-reminder hook
- luz-kubernetes overlay layout system — *is related to* — luz-env-config-reminder hook

%% ai-graph-end %%