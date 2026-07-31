---
ai_hash: 94a1b38c4cc8838c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-31
entities: []
source: vault PARA reorganization 2026-07-31
status: seedling
tags:
- obsidian
- plugins
- refactoring
title: Obsidian plugin configs pin folders to exact vault-root paths
type: gotcha
---

# Obsidian plugin configs pin folders to exact vault-root paths

Obsidian plugins store their working folder as a literal vault-root-relative path in `.obsidian/`, so relocating that folder during a reorganization breaks the plugin silently — no error, the feature just stops finding anything.

Two I hit while restructuring this vault:

- `.obsidian/templates.json` → `{"folder": "Templates"}`
- `.obsidian/plugins/obsidian-excalidraw-plugin/data.json` → `"folder": "Excalidraw"` and `"scriptFolderPath": "Excalidraw/Scripts"`

Both had to stay at the vault root even though every other root folder was being filed into PARA. The attachment folder is pinned the same way in `app.json`.

**Rule:** before moving any root-level folder, grep `.obsidian/*.json` and `.obsidian/plugins/*/data.json` for `folder`/`path` keys and treat every hit as pinned. If you must move one, update the config in the same commit.

Note that `app.json` here also sets `"alwaysUpdateLinks": true` — that only rewrites links for moves made *inside* Obsidian. Moves made with `git mv` or the shell bypass it entirely, so you own the link repair.

## Related

- [[Resolving a wikilink by basename truncates titles containing a slash]]
- [[Measure a broken-link baseline before a mass vault refactor]]

%% ai-graph-start %%

**Related notes:**
- [[Measure a broken-link baseline before a mass vault refactor]]
- [[Resolving a wikilink by basename truncates titles containing a slash]]
- [[Roadmap]]
- [[Precompute and validate a file-level move map before a mass reorganization]]

%% ai-graph-end %%