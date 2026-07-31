---
title: "polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)"
created: 2026-07-02
type: howto
status: seedling
source: "session 2026-07-02 — ran Polaris end-to-end"
tags: [polaris, cli, gotcha, mcp, vinnstack]
---

# polaris-cli 0.2.0 operational gotchas (bootstrap scope, status, up, agents)

Operational notes from actually running polaris-cli 0.2.0 end-to-end (2026-07-02, Windows):

- **`polaris bootstrap` defaults to `--scope user` (global)**, not project. It wires the polaris-mcp MCP server into `~/.mcp.json`, `~/.claude.json` (Claude Code), and VS Code `mcp.json`, and writes the `@polaris` pointer into `~/.claude/CLAUDE.md`. Use `--scope project` to wire into the current repo instead. `--yes` does NOT auto-start the tunnel (contrary to the README example) — it just prints "Run polaris up".

- **`polaris status` under-reports.** Its "wired" line only inspects the CURRENT directory for project-scope config (`.claude/settings.json` / `.cursor/mcp.json` with `polaris-mcp`), so after a user-scope bootstrap it still says "(not bootstrapped) / wired: none". Its "tunnel" line is accurate though — it uses a PID + TCP :3003 probe independent of cwd.

- **`polaris up` success check is too eager.** After starting the kubectl port-forward it waits only ~3s (6×500ms) for port 3003; a cold forward often answers a few seconds later, so it prints "Tunnel started but Polaris did not answer" while the tunnel is actually fine. Re-probe the port (Test-NetConnection 127.0.0.1 3003) or run `polaris status` — it will show "tunnel: on".

- **`polaris doctor` false-negatives gcloud/kubectl** in the npm-link dev build even when `where.exe gcloud/kubectl` finds them; the real `polaris up` gcloud calls (shelled) work anyway.

- **`polaris agents` / `polaris skills` are broken in 0.2.0** — they call the MCP tools `search_agents`/`search_skills` without the required `q` (query) string and accept no query argument, so both return an MCP -32602 validation error. Connectivity is fine; use the tools from the editor instead.

- The MCP endpoint `http://localhost:3003/mcp` returns **HTTP 405 to a plain GET** — expected for an MCP streamable-HTTP server (it answers the MCP POST handshake only). 405 = alive, not broken.

To actually USE Polaris: bootstrap + `polaris up`, then reload the editor (Claude Code / VS Code / Cursor) so it connects to the MCP server; invoke agents/skills there (e.g. `@polaris <question>`).

## Related

- [[3 Resources/Work-Side/Vinnstack/polaris-cli never writes ~.polarisstate.json — no reliable bootstrapped signal]]
