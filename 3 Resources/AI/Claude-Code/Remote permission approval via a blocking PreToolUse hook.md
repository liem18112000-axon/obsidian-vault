---
title: "Remote permission approval via a blocking PreToolUse hook"
created: 2026-06-18
type: howto
status: seedling
source: "session 2026-06-18"
tags: [claude-code, hooks, telegram, permissions]
---

# Remote permission approval via a blocking PreToolUse hook

A `PreToolUse` hook can **hold a tool call open** (block) and decide whether it runs by printing a verdict to stdout:

```json
{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"allow","permissionDecisionReason":"..."}}
```

`permissionDecision` is `allow` or `deny`. This lets you approve tool use from a remote channel: the blocking hook posts **Allow / Deny / Always** buttons to a chat (e.g. Telegram), then polls a decision file; a separate always-running daemon writes `<token>.decision` when a button is tapped, which unblocks the hook. It then emits the verdict JSON.

Why it works: the hook is synchronous, so Claude Code waits for it; the daemon is what is actually listening for the human tap.

Gotchas (each one bit during implementation):
- **`timeout` must exceed the internal wait.** The hook entry in `settings.json` needs `"timeout": 600` if the hook itself waits up to 180s for a tap — otherwise Claude Code kills the hook mid-prompt.
- **Make it opt-in at runtime** via a marker file. If always on, every Bash/Edit at your desk blocks waiting for a phone tap. A `/approvals on` command creates the marker; absent marker → hook exits 0 immediately.
- **Fail open, never wedge.** On timeout or any error, `exit 0` with NO stdout so the tool proceeds under Claudes normal permission flow.
- **"Always" cache keys Bash by first token** (e.g. `git`) so "always allow" does not whitelist *all* Bash.

Built for the [[Custom Telegram-Claude bridge vs official Claude Code Remote Control]] reverse channel.

## Related

- [[Custom Telegram-Claude bridge vs official Claude Code Remote Control]]
