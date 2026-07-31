---
title: "Polaris MCP tool catalog and usage pattern"
created: 2026-07-05
type: reference
status: seedling
source: "sessions 2026-07-05 / 2026-07-07"
tags: [polaris, mcp, tools, catalog, agents, skills]
aliases:
  - Polaris MCP Tools Inventory
---

# Polaris MCP tool catalog and usage pattern

Polaris is a catalog-router MCP server that exposes exactly **5 tools**. These are the only tools it surfaces — agents, skills, and files are content accessed *through* them.

## The 5 tools

| Tool | Purpose |
|------|---------|
| `search_agents(q, owner?, tags?, page?, limit?)` | Semantic search over runnable agent briefs. Entry point for *doing* something. |
| `load_agent(agent, request?, env?)` | Loads an agent brief by id/path and starts running it. `env` = `prod`, `dev-vn`, `test`, … The brief then pulls its own skills. |
| `search_skills(q, tags?, page?, limit?)` | Semantic search over skills — reusable building blocks agents compose. Not directly runnable. |
| `load_skill(id)` | Full detail (body, schema, metadata) for one skill, by UUID or name. |
| `load_file(path)` | Fetches a referenced file/artifact (`references/`, `scripts/`, …) so its contents can be read. |

## Usage pattern

```
search_agents(q=...) → pick an agent → load_agent(env=..., request=...)
                                          ↓ agent pulls dependencies on demand
                              search_skills / load_skill / load_file
```

The **agent brief** orchestrates which skills to pull — you don't call skills directly.

## Gotchas

- **No empty queries / no list-all.** `search_agents` and `search_skills` both reject an empty `q`, so the catalog cannot be dumped — any UI over Polaris is a search box, not a directory mirror. See [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].
- **`load_file` paths are catalog-rooted** (`skills/<id>/…`, `agents/<id>/…`), not relative to the calling skill — [[Polaris load_file paths are catalog-rooted, not skill-relative]].

## Related

- [[Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel|Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
- [[Polaris load_file paths are catalog-rooted, not skill-relative]]
