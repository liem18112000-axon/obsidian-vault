---
title: "vinnstack SKILL.md convention"
created: 2026-08-27
type: howto
status: seedling
source: "C:/Users/dvtliem/Kepler/vinnstack/vinnstack-skills — session 2026-08-27"
tags: [vinnstack, polaris, skill, convention, authoring]
---

# vinnstack SKILL.md convention

A Polaris/vinnstack **skill** is a folder under `vinnstack-skills/` (or a project `skills/` dir) containing a single **`SKILL.md`** — occasionally plus helper scripts (`run.sh`, `run_it.sh`, `ensure-bash.ps1`).

**Frontmatter (YAML):** `name`, `description` (a rich, detailed paragraph — this is what routes the skill, so it lists what it does, its rules, and trigger phrases), `version`; optional `metadata:` block with `source`, `audience: automation`, `tags: [...]`. The `metadata` block is optional (some runnable skills omit it).

**Body — two shapes:**
- *Judgement skills* (interrogate-qa, story-to-bdd-scenarios, grounded-bug-report): `## When to use` · `## Principle` · `## Process` (numbered steps) · `## Pitfalls` · `## Verification`. Optional `## Output contract`, `## Review lifecycle`.
- *Runnable/tool skills* (luz-docs-integration-test): `## Modes` · `## Inputs` · `## How to gather inputs` · `## How to invoke` (with bash) · `## What the script does` · `## Caveats`.

**Voice:** imperative, quality-first, "invents nothing", "cite the code/source", "dont drop silently". Location: `C:\Users\dvtliem\Kepler\vinnstack\vinnstack-skills`.

## Related

- [[A remote A2A agent needs its own connectors because MCP is client-side]]
