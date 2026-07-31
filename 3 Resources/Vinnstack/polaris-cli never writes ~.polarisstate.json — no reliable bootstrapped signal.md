---
title: "polaris-cli never writes ~/.polaris/state.json — no reliable bootstrapped signal"
created: 2026-07-02
type: lesson
status: seedling
source: "session 2026-07-02 — Polaris integration"
tags: [polaris, vinnstack, gotcha, cli]
---

# polaris-cli never writes ~/.polaris/state.json — no reliable bootstrapped signal

The polaris CLI (`polaris-cli`, @axonivy/polaris-cli) READS `~/.polaris/state.json` in its `status` command (for `kernelRef` + `version`) but **never writes it anywhere** — a full-repo grep finds only reads. So any integration that keys "is Polaris bootstrapped?" off `state.json` existence is perpetually false.

There is in fact **no reliable machine-wide "bootstrapped" signal** for default (MCP) mode:
- `state.json` — never written.
- `~/.polaris/cache` — only populated by `--offline` bootstrap (which downloads the kernel); default MCP bootstrap does no download, so cache stays empty.
- The real effect of `polaris bootstrap` is writing MCP config into the **current editor project** dir (`.claude/settings.json`, `.cursor/mcp.json`) or user-level files (`~/.mcp.json`, `~/.claude.json`) — i.e. cwd/user-scoped, not a global marker.

Consequence for the Vinnstack Polaris provider: "connected" is gated on **installed** (binary resolvable at `~/.polaris/bin/polaris(.exe)` or on PATH), NOT on bootstrapped — because installed is the only honest server-side global fact. Tunnel reachability (TCP :3003) and Google ADC presence are shown as detail.

Lesson: before making a file the source of truth for a state check, grep the tool that owns it to confirm something actually WRITES that file — a read-only artifact makes the check silently constant.

## Related

- [[Vinnstack auth providers: two patterns and the rule for adding one]]
