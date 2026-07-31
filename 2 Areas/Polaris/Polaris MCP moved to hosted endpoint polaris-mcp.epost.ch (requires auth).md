---
title: "Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)"
created: 2026-07-26
type: observation
status: seedling
source: "session 2026-07-26"
tags: [polaris, mcp, claude-code, epost]
---

# Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)

The **Polaris** AI-agents MCP server (routing agentic requests via `@polaris`) moved from a local port-forward tunnel at `http://localhost:3003/mcp` — previously started with `polaris up` — to a **hosted endpoint at `https://polaris-mcp.epost.ch/mcp`**. It is now configured as a single **user-scope** HTTP MCP server in `~/.claude.json`.

**Auth:** the hosted endpoint requires authentication. A direct request returns **HTTP 401 with no `WWW-Authenticate` header**, meaning it is *not* a standard OAuth auto-challenge — it expects a credential you supply (Bearer token / API key via a request header), or an OAuth flow triggered in-session with `/mcp`.

**Why it matters:** with the hosted endpoint, the old `polaris up` tunnel step (and the global `CLAUDE.md` note about it) is obsolete for connectivity — the remaining step is authenticating, not tunneling.

Changed on 2026-07-26.

## Related

- [[claude mcp list health status can be stale; verify MCP reachability with curl]]
