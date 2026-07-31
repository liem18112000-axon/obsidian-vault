---
ai_hash: 44cf9da1a154e354
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack claude auth logout)
status: seedling
tags:
- nodejs
- child_process
- cli
- stdin
- gotcha
- vinnstack
title: Spawning a prompting CLI hangs on open stdin — use stdio stdin ignore for EOF
type: lesson
---

# Spawning a prompting CLI hangs on open stdin — use stdio stdin ignore for EOF

Spawning a CLI that prompts for confirmation (e.g. `claude auth logout`) from a server **hangs** if the child inherits an open-but-empty stdin. Node's `child_process.execFile` (and `exec`) create stdin as a pipe and never end() it, so a process that reads stdin for a y/n prompt blocks forever — or until the `timeout` option kills it (which reads as 'stuck for N seconds then fails').

**Fix:** spawn with `stdio: ['ignore', 'pipe', 'pipe']` so the child's stdin is /dev/null → reads return EOF immediately → the prompt resolves with its default and the command completes. (Closing the pipe with `child.stdin.end()` also works, but 'ignore' is cleaner when you never write to it.)

Symptom in an app: a Logout/anything button that spins ~30s (the exec timeout) then errors, while the same command runs instantly in a real terminal (where a TTY answers the prompt).

Real case: Vinnstack `lib/authProviders.ts` — `claude auth logout` got stuck because the `run()` helper used execFile. Rewrote it to spawn with stdin ignored; login() already used stdin:'ignore' which is why it didn't hang.

%% ai-graph-start %%

**Related notes:**
- [[Node spawn shellfalse on Windows won't run .cmd.ps1 wrappers (ENOENT)]]
- [[Pass LLM prompts to spawned CLIs via stdin - Windows argv caps at 32K (ENAMETOOLONG)]]
- [[Driving a raw-mode or ink TTY prompt through a PTY needs carriage return, not newline, to submit]]
- [[Node child_process.kill on Windows doesn't kill descendant processes]]
- [[A silent canned fallback masks real failures — surface the underlying error]]

%% ai-graph-end %%