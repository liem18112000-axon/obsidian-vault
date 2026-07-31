---
title: "Claude Code official remote-control surfaces (web, Remote Control, Dispatch)"
created: 2026-06-19
type: reference
status: seedling
source: "research 2026-06-19"
tags: [claude-code, remote-control, reference]
---

# Claude Code official remote-control surfaces (web, Remote Control, Dispatch)

As of mid-2026 Claude Code has THREE distinct official ways to run/steer it remotely, and the axis that separates them is *where the agent actually executes*:

1. **Claude Code on the web** (claude.ai/code, Oct 2025) — the agent runs in an **Anthropic-managed cloud VM** (≈4 vCPU/16 GB), repo cloned from GitHub. NOT your machine. Good for parallel, sandboxed cloud tasks; auto-opens PRs. Needs GitHub + a paid plan. Terminal shortcuts: `claude --remote "task"` spins up a cloud session; `/teleport` (`/tp`) pulls a cloud session down into your local terminal.
2. **Remote Control** (Feb 2026, research preview, needs Claude Code v2.1.51+) — continue a session running on **YOUR machine** from a phone/browser. `claude remote-control` / `/remote-control` prints a session URL + QR; open in the Claude iOS/Android app or claude.ai. **Outbound HTTPS only, no inbound ports**; auth via claude.ai OAuth (no API keys); mobile push on finish/decision. The local `claude` process must stay running. Pro/Max/Team/Ent (Team/Ent admin must enable).
3. **Dispatch** — message a task from the Claude mobile app that spawns a session on your paired **Desktop** machine.

Key contrast for any 'remote Claude' discussion: web = cloud sandbox (can't touch your real files); Remote Control / Dispatch = your real local environment. Our DIY [[Telegram remote-control bridge for Claude Code]] occupies the same niche as Remote Control but over a free Telegram bot.

Docs: code.claude.com/docs/en/remote-control · code.claude.com/docs/en/claude-code-on-the-web

## Related

- [[Telegram remote-control bridge for Claude Code]]
