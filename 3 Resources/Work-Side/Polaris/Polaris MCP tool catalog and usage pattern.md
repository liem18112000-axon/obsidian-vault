---
title: "Polaris MCP tool catalog and usage pattern"
created: 2026-07-07
type: reference
status: seedling
source: "session 2026-07-07"
tags: [polaris, mcp, tools, catalog]
---

# Polaris MCP tool catalog and usage pattern

Polaris is a catalog-router that exposes exactly **5 MCP tools**. These are the only tools it surfaces — everything else (agents, skills, files) is content accessed *through* these tools.

## The 5 tools

| Tool | Purpose |
|------|---------|
| `search_agents` | Semantic search over runnable agent briefs. Entry point for *doing* something. Query `q` + optional `owner`, `tags`, pagination. |
| `load_agent` | Loads an agent brief by id/path and starts running it. Takes target `env` (e.g. `prod`, `dev-vn`, `test`) + the user `request`. |
| `search_skills` | Semantic search over skills — reusable building blocks that agents compose. Not directly runnable. |
| `load_skill` | Returns full detail (body, schema, metadata) for one skill by UUID or name. |
| `load_file` | Fetches a referenced file/artifact by path so its contents can be read. |

## Usage pattern

```
search_agents(q=...) → pick an agent → load_agent(env=..., request=...)
                                          ↓ agent pulls dependencies on demand
                              search_skills / load_skill / load_file
```

The **agent brief** (loaded by `load_agent`) orchestrates which skills to pull — you don't call skills directly.

## Related
- [[Polaris MCP tunnel startup and troubleshooting]]

## Related

- [[Polaris MCP tunnel startup and troubleshooting]]
