---
title: "Claude Code speaks MCP, not A2A — an A2A agent must be bridged to be used"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga USING-FROM-CLAUDE"
tags: [a2a, mcp, claude-code, architecture, gotcha]
---

# Claude Code speaks MCP, not A2A — an A2A agent must be bridged to be used

Claude Code natively calls **MCP** tools (Claude → tools). It has **no built-in A2A client**. So a remote **A2A** agent (agent → agent, JSON-RPC over HTTPS) is just an HTTP endpoint to Claude Code — it will NOT be auto-selected or called on its own.

Consequence/gotcha: a generic request like "gather context for TICKET-123" makes Claude use the tools it ALREADY has (e.g. a connected Atlassian MCP) and its own reasoning — NOT your deployed A2A agent. People assume "local Claude drives the A2A agent" happens automatically; it does not.

To actually route to an A2A agent from Claude Code, BRIDGE it one of four ways:
1. **Explicit instruction** — tell Claude to POST message/send to the URL with the bearer, then read results.
2. **Shell shortcut** — a function that curls message/send; run it yourself, paste output to Claude.
3. **Project skill / slash-command** — a SKILL.md that runs the curl deterministically (`/gather X`).
4. **MCP proxy** — a tiny MCP server exposing a tool (e.g. `gather_knowledge`) that calls the A2A endpoint, so it appears as a native tool. Even then, name it explicitly so Claude does not pick a competing MCP (e.g. Atlassian) instead.

A2A = protocol for agents to delegate to each other (agent cards, tasks, artifacts). MCP = protocol for a model/host to call tools+data. Different layers; Claude Code implements the MCP client side, not an A2A client.

Context: kga test-agent (LUZ-159671) — the deployed gather agent is A2A; driving it from Claude Code needs a bridge.

## Related

- [[A remote A2A agent needs its own connectors because MCP is client-side]]
