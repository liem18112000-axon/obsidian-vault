---
title: "Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001"
created: 2026-06-16
type: howto
status: seedling
source: "session 2026-06-16"
tags: [excalimate, mcp, setup, skills]
---

# Running Excalimate locally: skills in ~/.claude/skills plus MCP server on port 3001

To run Excalimate locally with live preview:

1. **Skills** — the 16 Excalimate skill folders (each `<name>/SKILL.md` + `references/`) from `github.com/excalimate/excalimate` under `skills/` go into `C:\Users\dvtliem\.claude\skills\` (global, editable copies): excalimate-core, animation-patterns, animated-presentations, architecture-diagrams, comparison-diagrams, data-pipelines, diagram-theming, er-diagrams, explainer-animations, export-optimization, flowcharts, mind-maps, network-topologies, org-charts, sequence-diagrams, timeline-roadmaps. They coexist with the separate `excalidraw-diagram` skill (no name collision).
2. **MCP server** — `npx -y @excalimate/mcp-server` (package `@excalimate/mcp-server`, bin `excalimate-mcp`). Listens on `http://localhost:3001/mcp` and serves live-preview SSE at `http://localhost:3001/live`. Foreground process: it stops when its session ends, so re-run it whenever live preview is wanted.
3. **Register once** — `claude mcp add --transport http --scope user excalimate http://localhost:3001/mcp` (writes `C:\Users\dvtliem\.claude.json`; user scope = all projects).
4. **Live preview** — start the server, open app.excalimate.com, click the **Live** button; the agent (loaded with the skills) builds diagrams that render live.

Gotchas: [[MCP servers load only at Claude Code startup; skills hot-reload]] and [[A 406 from curl on an MCP /mcp endpoint is normal]]. Backdrop: [[Excalimate is AI skills plus an optional MCP server, not one app]].

## Related

- [[Excalimate is AI skills plus an optional MCP server]]
- [[not one app]]
- [[MCP servers load only at Claude Code startup; skills hot-reload]]
- [[A 406 from curl on an MCP /mcp endpoint is normal]]
