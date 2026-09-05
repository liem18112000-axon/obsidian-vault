---
title: "MCP tools load at client startup: registering mid-session doesn't expose them until restart"
created: 2026-08-29
type: lesson
tags: [claude-code, mcp, gotcha]
---

# MCP tools load at client startup: registering mid-session doesn't expose them until restart

`claude mcp add <server>` writes config and `claude mcp list` may immediately show the server as "✔ Connected" — but the running Claude Code session's TOOL registry is snapshotted at startup. So the new server's tools are NOT callable in the current session; a restart is required for them to appear.

Verified: after registering test-plan-definition mid-session (list shows ✔ Connected), ToolSearch for `mcp__test-plan-definition__define_plan` returned "No matching deferred tools found". Only after a restart do the tools load.

Implication: when a flow depends on a just-added MCP server's tools, tell the user to restart Claude Code; "Connected" in `claude mcp list` is necessary but not sufficient for in-session tool availability. (Same restart also picks up new server-side code after a bridge redeploy, and new `.claude/commands/*` / project `CLAUDE.md`.)
