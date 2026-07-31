---
title: "Excalimate is AI skills plus an optional MCP server, not one app"
created: 2026-06-16
type: concept
status: seedling
source: "session 2026-06-16"
tags: [excalimate, mcp, diagrams, skills]
---

# Excalimate is AI skills plus an optional MCP server, not one app

Excalimate is three loosely-coupled layers, not a single hosted app. (1) A static web SPA at app.excalimate.com where diagrams render, animate, and are edited entirely in-browser. (2) An **optional** MCP server (`@excalimate/mcp-server`) used only for AI integration and live preview. (3) **16 SKILL.md files** that teach an AI agent how to build good animated diagrams, which it then creates in the app via the MCP server.

The skills are the part that matters most for output quality, and they live on the *agent* side (loaded as context), not inside the hosted app. They work independently of the MCP server — the server just enables the live in-app preview. So "installing Excalimate skills" and "running the MCP server" are two separate acts.

See [[Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001]].

## Related

- [[Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001]]
