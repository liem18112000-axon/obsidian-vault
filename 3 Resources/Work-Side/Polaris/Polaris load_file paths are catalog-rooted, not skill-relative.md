---
ai_hash: 894010496a97e472
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- polaris
- mcp
- load_file
- gotcha
- paths
title: Polaris load_file paths are catalog-rooted, not skill-relative
type: lesson
---

# Polaris load_file paths are catalog-rooted, not skill-relative

Gotcha (Vinnstack Polaris, 2026-07-14): the MCP load_file(path) tool resolves paths from the CATALOG ROOT (e.g. agents/log-analyzer/AGENT.md, skills/gcp-fetch-logs/SKILL.md), but a skill/agent BODY references its own files RELATIVELY (references/foo.md, scripts/bar.sh). Passing the bare relative ref to load_file returns {"error":"not_found"}.

Fix: prefix a relative ref with the item's root — `${mode}/${id}/${ref}` where mode is skills|agents and id is the skill/agent id — unless it already starts with skills/ or agents/. Verified: skills/gcp-fetch-logs/references/future-direction.md → 828 bytes; the bare path → not_found.

Convention: skills live under skills/<id>/, agents under agents/<id>/ (matches the load_agent example filePath agents/log-analyzer/AGENT.md). Related: [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].

%% ai-graph-start %%

**Related notes:**
- [[Polaris MCP tool catalog and usage pattern]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]

%% ai-graph-end %%