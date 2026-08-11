---
ai_hash: d945dfaf2e519fe5
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-14
entities:
- MCP server
- TCP probe
- Polaris tunnel state
- App
- Vinnstack PR-B
- polarisCli.polarisStatus
- tcpReachable
- polaris up (command)
- polaris down (command)
- polaris status (command)
- POST /mcp initialize (API endpoint)
- HTTP 200
- /api/polaris/skills?q=gcloud (API endpoint)
- Turn off (action)
- CLI
- App indicator
- MCP endpoint reachability
- PR-C search
- PR-D tool-calls
- Reachable/MCP reachable (label)
- Vinnstack config
- config.ts
- polarisEnabled
- config.json
- App restart
- Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded
  (note)
- Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the
  CLI (note)
- 127.0.0.1:3003 (address)
- Verification finding
source: session 2026-07-14 PR-B verify
status: seedling
tags:
- polaris
- mcp
- gotcha
- verification
- vinnstack
title: Polaris :3003 MCP server is persistent — TCP probe not equal to polaris tunnel
  state
type: observation
---

# Polaris :3003 MCP server is persistent — TCP probe not equal to polaris tunnel state

Verification finding (2026-07-14, Vinnstack PR-B): the app decides 'Polaris tunnel on/off' with a raw TCP probe of 127.0.0.1:3003 (polarisCli.polarisStatus → tcpReachable). But in the observed environment the **MCP HTTP server on :3003 is persistent and independent of `polaris up`/`polaris down`**. Proof: with `polaris status` reporting the tunnel OFF, :3003 stayed OPEN for 12s+, `POST /mcp initialize` returned HTTP 200, and /api/polaris/skills?q=gcloud still returned 19 items.

Consequence: after clicking 'Turn off', `polaris down` DOES run (CLI confirms off) but the app's indicator stays 'on' because the port keeps listening → the card looks like Turn-off failed. The probe actually measures 'is the MCP endpoint reachable' (which is what PR-C search / PR-D tool-calls truly need), NOT 'polaris tunnel state per `polaris status`'.

Fix options: (A) parse `polaris status` for authoritative tunnel state; (B, recommended) relabel the indicator to 'Reachable/MCP reachable' since reachability is the functional truth; (A+B) show both lines.

Also: Vinnstack config is process-cached (config.ts read() memoizes), so `polarisEnabled=false` and any config.json change needs an app restart to take effect. Related: [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]], [[Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI]].

## Related

- [[Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded]]

%% ai-graph-start %%

**Related notes:**
- [[polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)]]
- [[Wiring an external MCP-serving CLI into a Next.js app status-on-provider, actions-on-dedicated-route]]
- [[Vinnstack Polaris integration is three passive touchpoints]]
- [[polaris-cli never writes ~.polarisstate.json — no reliable bootstrapped signal]]
- [[Polaris 0.2.0 serves agentsskillsrules over an MCP tunnel]]

**Relations:**
- MCP server — *is* — persistent
- TCP probe — *does not equal* — Polaris tunnel state
- App — *determines tunnel state via* — TCP probe
- TCP probe — *targets* — 127.0.0.1:3003 (address)
- 127.0.0.1:3003 (address) — *hosts* — MCP server
- polarisCli.polarisStatus — *yields* — tcpReachable
- MCP server — *is independent of* — polaris up (command)
- MCP server — *is independent of* — polaris down (command)
- polaris status (command) — *reports* — tunnel OFF
- MCP server — *remains OPEN when* — Polaris tunnel state
- POST /mcp initialize (API endpoint) — *returns* — HTTP 200
- /api/polaris/skills?q=gcloud (API endpoint) — *returns* — 19 items
- polaris down (command) — *executes after* — Turn off (action)
- CLI — *confirms* — polaris down (command)
- App indicator — *stays 'on' because* — MCP server
- MCP server — *keeps listening* — App indicator
- TCP probe — *measures* — MCP endpoint reachability
- PR-C search — *requires* — MCP endpoint reachability
- PR-D tool-calls — *requires* — MCP endpoint reachability
- TCP probe — *does not measure* — Polaris tunnel state
- Fix option A — *is to parse* — polaris status (command)
- Fix option B — *is to relabel* — App indicator
- App indicator — *to* — Reachable/MCP reachable (label)
- Vinnstack config — *is* — process-cached
- config.ts — *memoizes* — read()
- polarisEnabled — *requires* — App restart
- config.json — *requires* — App restart
- Verify-after-mutate re-probe external state before reporting a CLI toggle succeeded (note) — *is related to* — MCP server
- Polaris MCP is search-only 5 tools, no list-all, driven over HTTP JSON-RPC not the CLI (note) — *is related to* — MCP server
- Vinnstack PR-B — *identified* — Verification finding
- Verification finding — *occurred on* — 2026-07-14

%% ai-graph-end %%