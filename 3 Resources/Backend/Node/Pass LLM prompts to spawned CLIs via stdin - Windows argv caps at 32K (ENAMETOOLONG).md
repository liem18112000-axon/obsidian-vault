---
ai_hash: 0ca4aaccad5d016a
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack approve ENAMETOOLONG
status: seedling
tags:
- nodejs
- windows
- spawn
- claude-cli
- vinnstack
title: Pass LLM prompts to spawned CLIs via stdin - Windows argv caps at 32K (ENAMETOOLONG)
type: gotcha
---

# Pass LLM prompts to spawned CLIs via stdin - Windows argv caps at 32K (ENAMETOOLONG)

Spawning `claude -p "<huge prompt>"` (or any CLI) with the prompt as a positional argv element works until the prompt grows: on Windows the ENTIRE command line is capped at ~32,767 chars, and Node's spawn fails with `spawn ENAMETOOLONG`. Document-sized prompts (a PRD embedded twice: comment body + revision source) cross the cap easily, so the failure appears only for the LARGEST artifacts - small generations keep working, which masks the bug as flaky.

Fix: send the prompt via stdin - `claude -p` reads the user prompt from stdin when no positional prompt is given. In Node: spawn with `stdio: ["pipe", ...]` and `child.stdin.end(prompt)`. Keep only bounded strings (flags, model ids, a system-prompt of known size) on argv.

Related trap in the same code: a "sanitize args" guard that REJECTS oversized args (fail loudly) is better than one that drops them silently - a dropped positional prompt makes the CLI answer an empty prompt and callers may trust the junk output.

## Related

- [[Idempotency guards keyed on object presence break when hydration materializes the object]]

%% ai-graph-start %%

**Related notes:**
- [[Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]
- [[Headless claude -p loads the user global CLAUDE.md; isolate per-selection AI transforms with delimiters + data-guard]]
- [[Spawning a prompting CLI hangs on open stdin — use stdio stdin ignore for EOF]]
- [[vinnstack spawns the local claude CLI for subscription-authenticated automation]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]

%% ai-graph-end %%