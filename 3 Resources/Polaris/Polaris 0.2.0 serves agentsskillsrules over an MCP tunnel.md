---
title: "Polaris 0.2.0 serves agents/skills/rules over an MCP tunnel"
created: 2026-07-14
type: concept
status: seedling
source: "session 2026-07-14"
tags: [polaris, mcp, epost, claude-code]
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
