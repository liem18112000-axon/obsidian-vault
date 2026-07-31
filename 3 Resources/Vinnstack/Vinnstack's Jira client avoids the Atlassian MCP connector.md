---
title: "Vinnstack's Jira client avoids the Atlassian MCP connector"
created: 2026-07-12
type: lesson
status: seedling
source: "session 2026-07-12 — writing doc/vinnstack-bdd-pipeline.html"
tags: [vinnstack, jira, mcp, integration, gotcha]
---

# Vinnstack's Jira client avoids the Atlassian MCP connector

Vinnstack's Jira integration (lib/jira/jiraClient.ts) is a hand-rolled REST client (Basic auth via ATLASSIAN_EMAIL/ATLASSIAN_API_TOKEN) built specifically to avoid using claude.ai's Atlassian MCP connector for reads/writes in its Interrogation Room and BDD pipelines. The stated reason in the code's own comments: the MCP connector "binds to one cloud site and may silently miss the epic's actual site."

This is a concrete example of rejecting a seemingly-convenient MCP integration in favor of a direct REST client when the MCP tool's implicit scoping assumptions (single-site binding) don't match a real multi-site or ambiguous-site requirement. Worth recalling before reaching for an MCP connector as a shortcut in any project that might span multiple instances/sites of the same SaaS product.

## Related
[[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
