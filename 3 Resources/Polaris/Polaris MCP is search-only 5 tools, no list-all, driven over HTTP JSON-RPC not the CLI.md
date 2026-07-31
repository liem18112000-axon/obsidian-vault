---
title: "Polaris MCP is search-only: 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI"
created: 2026-07-14
type: observation
status: seedling
source: "session 2026-07-14 WI-0 spike"
tags: [polaris, mcp, json-rpc, gotcha, epost]
---

# Polaris MCP is search-only: 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI

WI-0 spike (2026-07-14) against Polaris MCP (`polaris-mcp-server` v1.0.0, HTTP JSON-RPC at localhost:3003/mcp):

**The catalog is search-only — there is no 'list all'.** `search_skills` and `search_agents` both REQUIRE a non-empty query `q` and explicitly refuse an empty one. So any UI over Polaris must be a search box → results, NOT a full list like a directory mirror.

The 5 MCP tools: `search_skills(q,tags?,page?,limit?)`, `search_agents(q,tags?,owner?,…)`, `load_skill(id)`, `load_file(path)`, `load_agent(agent,request?,env?)` (returns an orchestration brief).

**Drive it over HTTP JSON-RPC, not the CLI.** In 0.2.0 the `polaris agents`/`polaris skills` CLI commands are broken — they call the MCP search tools without wiring the required `q`, so they always error with a Zod validation failure. Calling the MCP endpoint directly (initialize → grab Mcp-Session-Id header → notifications/initialized → tools/call) is less code and more robust than spawning+parsing the CLI.

**Result shape gotcha:** tools/call returns `{result:{content:[{type:'text',text:'<a JSON string>'}]}}` — the inner text must be JSON.parse'd AGAIN to get `{items:[…]}`. SSE responses may prefix lines with `data: `.

**`status` never says 'bootstrapped'** even right after a successful bootstrap — don't gate app logic on it. Bootstrap mutates ~/.claude/CLAUDE.md, ~/.mcp.json, ~/.claude.json, and VS Code mcp.json.

Full reference: doc/polaris/polaris-mcp-surface.md. Related: [[Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel]], [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]].

## Related

- [[Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel]]
