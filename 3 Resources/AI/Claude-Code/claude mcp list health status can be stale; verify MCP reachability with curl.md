---
ai_hash: 178c1df36365fcb4
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-26
entities: []
source: session 2026-07-26
status: seedling
tags:
- claude-code
- mcp
- gotcha
- debugging
title: claude mcp list health status can be stale; verify MCP reachability with curl
type: lesson
---

# claude mcp list health status can be stale; verify MCP reachability with curl

The `claude mcp list` / `claude mcp get` **health status can be stale or misleading** — it may report `ConnectionRefused: Unable to connect` for an endpoint that is actually up. In one case the polaris endpoint showed `ConnectionRefused` while it was genuinely reachable and returning **HTTP 401**.

**Verify true reachability with a direct curl** instead of trusting the CLI status:

```bash
curl -sS -o /dev/null -w "HTTP %{http_code} | connect=%{time_connect}s\n" \
  -X POST https://host/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d "{\"jsonrpc\":\"2.0\",\"id\":1,\"method\":\"initialize\",\"params\":{}}"
```

Interpretation: **HTTP 401 = reachable + needs auth** (not down). A genuine `ConnectionRefused`/timeout means the host/port is truly unreachable.

**Related gotcha — multi-scope conflicts:** an MCP server defined in more than one scope (e.g. `user` in `~/.claude.json` vs `project` in `.mcp.json`) with *different* URLs triggers a "defined in multiple scopes with different endpoints" warning, and the health check may probe the wrong (stale) one. OAuth tokens are stored per-endpoint, so auth in one scope will not carry to the other. **Consolidate to a single scope** with `claude mcp remove <name> -s <scope>`, then re-add on the surviving scope.

## Related

- [[Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)]]

%% ai-graph-start %%

**Related notes:**
- [[Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)]]
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[A 406 from curl on an MCP mcp endpoint is normal]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]
- [[A globally-bootstrapped MCP server loads into every headless claude spawn]]

%% ai-graph-end %%