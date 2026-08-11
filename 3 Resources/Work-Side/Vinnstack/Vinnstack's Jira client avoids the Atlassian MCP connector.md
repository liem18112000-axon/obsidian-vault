---
ai_hash: 8136757befe4a066
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: session 2026-07-12 — writing doc/vinnstack-bdd-pipeline.html
status: seedling
tags:
- vinnstack
- jira
- mcp
- integration
- gotcha
title: Vinnstack's Jira client avoids the Atlassian MCP connector
type: lesson
---

# Vinnstack's Jira client avoids the Atlassian MCP connector

Vinnstack's Jira integration (lib/jira/jiraClient.ts) is a hand-rolled REST client (Basic auth via ATLASSIAN_EMAIL/ATLASSIAN_API_TOKEN) built specifically to avoid using claude.ai's Atlassian MCP connector for reads/writes in its Interrogation Room and BDD pipelines. The stated reason in the code's own comments: the MCP connector "binds to one cloud site and may silently miss the epic's actual site."

This is a concrete example of rejecting a seemingly-convenient MCP integration in favor of a direct REST client when the MCP tool's implicit scoping assumptions (single-site binding) don't match a real multi-site or ambiguous-site requirement. Worth recalling before reaching for an MCP connector as a shortcut in any project that might span multiple instances/sites of the same SaaS product.

## Related
[[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]

%% ai-graph-start %%

**Related notes:**
- [[Atlassian MCP connector binds to one cloud site, which can differ from your REST token's site]]
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[Headless claude exit 0 does not mean the operation succeeded]]
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]
- [[Vinnstack Polaris integration is three passive touchpoints]]

%% ai-graph-end %%