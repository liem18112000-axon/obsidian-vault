---
ai_hash: b212d222cf3ad9ca
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities: []
source: session 2026-07-14
status: seedling
tags:
- polaris
- mcp
- epost
- claude-code
title: Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel
type: concept
---

# Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel

Polaris (ePost) 0.2.0 uses an **MCP model**: after `polaris up` it serves its agents, skills, and rules to editors over an MCP tunnel (the server shows up as `mcp__polaris*` in `~/.claude.json`). The old behavior — writing agents/skills/rules to disk in an IDE format — is now the *legacy* path, reached via `polaris bootstrap --offline` or `polaris convert --target <ide>`.

Key facts:
- **Auth is Google Cloud ADC** — the same credentials the gcloud login establishes; there is no separate Polaris token.
- Tunnel default local port is **3003** (`POLARIS_MCP_LOCAL_PORT`). `polaris agents` / `polaris skills` require the tunnel to be up.
- `bootstrap` takes `--persona <developer|contributor|service|stakeholder>` and `--scope <user|project>`.
- `polaris run <agent>` fetches an agent's **orchestration brief** (usable headless with `--headless --yes`).

Why it matters: any app embedding Polaris must decide whether to consume it live over MCP (needs the tunnel running) or materialize it offline via `convert` for headless/air-gapped use.

## Related

- [[Vinnstack Polaris integration is three passive touchpoints]]

%% ai-graph-start %%

**Related notes:**
- [[Polaris MCP moved to hosted endpoint polaris-mcp.epost.ch (requires auth)]]
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[Making Polaris MCP tools reachable by Vinnstack's spawned agent (discovery + allowlist)]]
- [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]]

%% ai-graph-end %%