---
title: Claude Code hooks event model
created: 2026-06-11
type: concept
status: seedling
source: research session 2026-06-11
tags:
  - claude-code
  - hooks
  - accesstrade
  - automation
  - concept
aliases:
  - Claude hooks
  - Claude Code hook events
  - PreToolUse PostToolUse
  - Affiliate automation hook patterns
  - Affiliate hooks
  - Accesstrade hook patterns
---

# Claude Code hooks event model

**Hooks are shell/HTTP commands Claude Code runs *deterministically* at defined lifecycle events — they fire whether or not the model "decides" to, which is exactly what you want for guardrails and automation that must always happen.** Where a skill is advisory (the model may or may not use it), a hook is enforced.

## Events, and the affiliate job each one does

The high-value hooks fall into three jobs: **ground** Claude in fresh data, **guard** money-moving actions, **capture** outcomes.

| Event | Fires when… | Affiliate use |
|---|---|---|
| `SessionStart` | session begins/resumes | preload campaign list; inject an earnings snapshot via `hookSpecificOutput.additionalContext` |
| `UserPromptSubmit` | you submit a prompt | inject today's earnings snapshot as context |
| `PreToolUse` | before a tool runs (can block) | deny `product_link/create` against a campaign that isn't `RUNNING` or that you aren't approved on — the most common cause of *unpaid* clicks |
| `PostToolUse` | after a tool succeeds | append every minted `aff_link` + `sub1` to a CSV ledger |
| `Stop` | Claude finishes responding | one-line session summary / trigger the daily digest skill |
| `Notification` | Claude sends a notification | forward to Slack/Telegram/Zalo (installers for all three live in this vault) |

(There are many more — `SubagentStop`, `PreCompact`, `SessionEnd`, etc.)

An `http`-type handler can also act as a **postback sink**: point Accesstrade's [[Accesstrade postback and S2S conversion tracking|postback URL]] at it to enrich SubID -> content mappings and notify in real time.

## Configuration shape (`settings.json`)

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          { "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/log-link.sh",
            "if": "Bash(*product_link*)", "timeout": 30 }
        ]
      }
    ]
  }
}
```

- **matcher** filters by tool (`Bash`, `Edit|Write`, regex, or `*`).
- Handler **types**: `command`, `http`, `mcp_tool`, `prompt`, `agent`.

## How a hook talks back

```mermaid
flowchart TD
    E[Event fires] --> H[Hook command runs]
    H --> X{Exit code}
    X -- 0 --> J[stdout parsed as JSON]
    X -- 2 --> B[BLOCK; stderr fed to Claude]
    X -- other --> N[non-blocking error, continue]
    J --> D["JSON: decision/continue/<br/>additionalContext/<br/>hookSpecificOutput"]
```

- **Exit 0** → stdout may be JSON: `additionalContext` (inject info), `decision: "block"` + `reason`, `continue: false`.
- **Exit 2** → hard block; stderr is shown to Claude.
- `PreToolUse` uses `hookSpecificOutput.permissionDecision` = `allow` / `deny` / `ask`.

## Scheduling caveat

Hooks fire on *Claude's* lifecycle, not the clock. For time-based jobs (a daily pull) use the OS scheduler or the `schedule`/`loop` skills to launch a session, and let `SessionStart`/`Stop` hooks do the wiring.

## Related

- [[Claude Code Skill anatomy]]
- [[Skills vs Hooks vs MCP vs subagents]]
- [[Accesstrade postback and S2S conversion tracking]]
- [[Secrets handling for affiliate API keys]]
- [[Use case - automated daily conversion digest]]
- [[Accesstrade API Integration - MOC]]
