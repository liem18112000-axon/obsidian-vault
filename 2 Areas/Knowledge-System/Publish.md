---
ai_hash: cf58eba12f782571
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-05-27
entities: []
status: seedling
tags:
- obsidian
- quartz
- publishing
- git-submodule
- knowledge-management
title: publish.sh ships the vault to the Quartz site in one command
type: howto
---

# publish.sh ships the vault to the Quartz site in one command

`/c/quartz/publish.sh` is the single entry point for getting vault changes onto the published Quartz site. The vault is a **git submodule** of the Quartz repo (`C:\quartz`), so publishing is always two commits — the vault, then the submodule pointer — and the script does both.

```bash
/c/quartz/publish.sh                       # auto message: "vault update 2026-05-25 23:07"
/c/quartz/publish.sh "add stage 7 notes"   # custom message, used for BOTH repos
```

It is idempotent across the three possible states:

| State | What it does |
|---|---|
| Vault dirty | commits + pushes the vault, bumps the submodule, pushes Quartz |
| Vault clean, submodule behind | bumps the submodule, pushes Quartz |
| Already in sync | prints `nothing to publish`, exits 0 |

Run it from anywhere — the path is absolute. Optional convenience never set up: a `Makefile` with `make publish` in `C:\quartz`, or a shell `alias publish='/c/quartz/publish.sh'`.

## Related

- [[My knowledge ecosystem Claude hooksskills - Obsidian vault - Quartz wiki + vault-graph (Vertex Graph RAG)]]

%% ai-graph-start %%

**Related notes:**
- [[obsidian-quartz publishes via a content submodule pointer bump]]
- [[My knowledge ecosystem Claude hooksskills - Obsidian vault - Quartz wiki + vault-graph (Vertex Graph RAG)]]
- [[Classify local vs upstream with git merge-base to pick ff or rebase]]
- [[git submodule update --remote can clobber unpushed submodule HEAD]]
- [[Stage 6 — Advanced Automation]]

%% ai-graph-end %%