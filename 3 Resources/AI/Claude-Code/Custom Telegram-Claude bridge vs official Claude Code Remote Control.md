---
ai_hash: 9c52491a1ea2c9b6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-18
entities: []
source: session 2026-06-18
status: seedling
tags:
- claude-code
- telegram
- remote-control
- reference
title: Custom Telegram-Claude bridge vs official Claude Code Remote Control
type: concept
---

# Custom Telegram-Claude bridge vs official Claude Code Remote Control

A homegrown Telegram→Claude bridge (a notify hook for Stop/Notification + a long-poll daemon that runs headless `claude --resume -p` and streams output back) can do several things the **official `claude remote-control`** cannot:

- **API-key auth works.** Official RC requires claude.ai OAuth on Pro/Max/Team/Enterprise; API keys are rejected.
- **Survives an org policy block.** Official RC can be disabled by a Team/Enterprise admin toggle; the bridge is independent.
- **Survives closing the terminal.** Official RC dies when the `claude` process exits; the bridge resumes sessions on demand, so there is nothing to keep alive.
- **No ~10-minute network-outage timeout** (official RC times out).
- **Coexists with ultraplan** (official RC disconnects when ultraplan starts — both want the claude.ai/code surface).
- **Many sessions/projects from one chat** (official RC = one remote session per interactive process outside server mode).
- **No version floor** (official RC needs Claude Code v2.1.51+).
- **Permission approvals as inline chat buttons** without the Claude mobile app — see [[Remote permission approval via a blocking PreToolUse hook]].

**Tradeoff / what it gives up:** the bridge runs *separate headless turns* of a session (`claude --resume -p`). It does NOT steer the live interactive terminal session you are watching. Tmux/PTY-injection bots (ccgram, JessyTsui Claude-Code-Remote) and official RC *do* drive the live session — but injection is Unix-oriented and brittle on Windows, so headless-resume is the better fit there.

Reference points: official docs `code.claude.com/docs/en/remote-control`; community bots `github.com/jsayubi/ccgram`, `github.com/JessyTsui/Claude-Code-Remote`.

## Related

- [[Remote permission approval via a blocking PreToolUse hook]]
- [[3 Resources/AI/Claude-Code/Windows claude subprocess is a process tree — taskkill T to reap it]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Code official remote-control surfaces (web, Remote Control, Dispatch)]]
- [[Remote permission approval via a blocking PreToolUse hook]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]
- [[Windows claude subprocess is a process tree — taskkill T to reap it]]
- [[vinnstack spawns the local claude CLI for subscription-authenticated automation]]

%% ai-graph-end %%