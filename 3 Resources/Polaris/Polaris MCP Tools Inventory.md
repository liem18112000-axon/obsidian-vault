---
title: "Polaris MCP Tools Inventory"
created: 2026-07-05
type: concept
status: seedling
source: "session 2026-07-05"
tags: [polaris, mcp, tools, agents, skills]
---

# Polaris MCP Tools Inventory

Polaris is a catalog-router MCP server that exposes 5 tools. You search for agents/skills, then load and run them on demand -- Claude assembles the procedure from the brief.

## The 5 Tools

| Tool | Purpose |
|------|---------|
| search_agents | Semantic search over runnable orchestration briefs. Free-text q + optional owner, tags, pagination. Entry point to run something. |
| load_agent | Loads an agent brief by id/path and starts executing it. Takes env (e.g. prod, dev-vn, test) and request. The brief then calls load_skill internally. |
| search_skills | Semantic search over skill artifacts -- reusable building blocks that agents compose (not directly runnable themselves). |
| load_skill | Returns full detail (body, schema, metadata) for one skill by UUID or name. |
| load_file | Fetches a single file by repo-root path -- used for references/, scripts/, or any file an agent/skill brief points to. |

## Workflow Pattern

search_agents -> load_agent (runs it) -> brief calls search_skills / load_skill / load_file

## Gotcha: No Empty Queries

Neither search_agents nor search_skills accepts an empty query -- you cannot dump the full catalog. Always search by topic (e.g. log analysis, MongoDB data prep, deploy luz-docs).

## Related

- [[Polaris GKE Tunnel Auth]] -- auth is required before polaris up can register these tools
- [[AGENTS]]
- [[SKILL]]

## Related

- [[Polaris GKE Tunnel Auth]]
- [[AGENTS]]
- [[SKILL]]
