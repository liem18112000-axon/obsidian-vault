---
ai_hash: 59ec676502f05dc3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
entities: []
---

﻿---
title: "Claude Code hooks cannot run slash commands or clear-compact; they only inject additionalContext"
created: 2026-06-02
type: lesson
status: seedling
source: "session 2026-06-02 handoff gate"
tags: [claude-code, hooks, slash-commands, automation, precompact]
---

# Claude Code hooks cannot run slash commands or clear-compact; they only inject additionalContext

Claude Code hooks communicate only through stdout, stderr, and exit codes. They **cannot execute slash commands** (`/session-handoff`, `/clear`, `/compact`), trigger tool calls, or run interactive prompts that wait for a user yes/no. The only way a hook can influence the session is to inject plain text via `hookSpecificOutput.additionalContext` (wrapped as a system reminder the model reads next turn) or to block with a `reason` (UserPromptSubmit/Stop `decision:"block"`, PreToolUse `permissionDecision:"deny"`, or exit code 2).

**Consequence for automation:** a hook can only ASK the model to do something. To "auto-handoff then clear at a context threshold", the hook injects an instruction; the model then asks the user for permission and runs the handoff skill - but **the model cannot run /clear either** (it is a built-in CLI command, excluded from the Skill tool), so the user must type /clear themselves.

**Note:** `PreCompact` (matcher `auto`|`manual`) fires before compaction and can block it with exit 2, but has no documented `additionalContext`, so it cannot instruct the model. See [[Claude Code hooks see no token usage in their payload; read the transcript usage entries instead]].

## Related

- [[Claude Code hooks see no token usage in their payload; read the transcript usage entries instead]]

%% ai-graph-start %%

**Related notes:**
- [[Claude Code hooks see no token usage in their payload; read the transcript usage entries instead]]
- [[Claude Code hooks event model]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]
- [[Remote permission approval via a blocking PreToolUse hook]]
- [[Claude Code Skill anatomy]]

%% ai-graph-end %%