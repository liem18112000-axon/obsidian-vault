---
title: "Polaris load_file paths are catalog-rooted, not skill-relative"
created: 2026-07-14
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [polaris, mcp, load_file, gotcha, paths]
---

# Polaris load_file paths are catalog-rooted, not skill-relative

Gotcha (Vinnstack Polaris, 2026-07-14): the MCP load_file(path) tool resolves paths from the CATALOG ROOT (e.g. agents/log-analyzer/AGENT.md, skills/gcp-fetch-logs/SKILL.md), but a skill/agent BODY references its own files RELATIVELY (references/foo.md, scripts/bar.sh). Passing the bare relative ref to load_file returns {"error":"not_found"}.

Fix: prefix a relative ref with the item's root — `${mode}/${id}/${ref}` where mode is skills|agents and id is the skill/agent id — unless it already starts with skills/ or agents/. Verified: skills/gcp-fetch-logs/references/future-direction.md → 828 bytes; the bare path → not_found.

Convention: skills live under skills/<id>/, agents under agents/<id>/ (matches the load_agent example filePath agents/log-analyzer/AGENT.md). Related: [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].
