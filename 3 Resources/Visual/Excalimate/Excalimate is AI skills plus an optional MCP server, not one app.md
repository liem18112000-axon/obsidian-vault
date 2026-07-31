---
ai_hash: 4d867f69f62d132c
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-16
entities: []
source: session 2026-06-16
status: seedling
tags:
- excalimate
- mcp
- diagrams
- skills
title: Excalimate is AI skills plus an optional MCP server, not one app
type: concept
---

# Excalimate is AI skills plus an optional MCP server, not one app

Excalimate is three loosely-coupled layers, not a single hosted app. (1) A static web SPA at app.excalimate.com where diagrams render, animate, and are edited entirely in-browser. (2) An **optional** MCP server (`@excalimate/mcp-server`) used only for AI integration and live preview. (3) **16 SKILL.md files** that teach an AI agent how to build good animated diagrams, which it then creates in the app via the MCP server.

The skills are the part that matters most for output quality, and they live on the *agent* side (loaded as context), not inside the hosted app. They work independently of the MCP server — the server just enables the live in-app preview. So "installing Excalimate skills" and "running the MCP server" are two separate acts.

See [[3 Resources/Visual/Excalimate/Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]].

## Related

- [[3 Resources/Visual/Excalimate/Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]

%% ai-graph-start %%

**Related notes:**
- [[Running Excalimate locally skills in ~.claudeskills plus MCP server on port 3001]]
- [[Excalimate export is browser-only; headless export needs Playwright + share URL]]
- [[A 406 from curl on an MCP mcp endpoint is normal]]
- [[Excalimate cloud share links are CORS-broken — use Connect to MCP server instead]]
- [[MCP servers load only at Claude Code startup; skills hot-reload]]

%% ai-graph-end %%