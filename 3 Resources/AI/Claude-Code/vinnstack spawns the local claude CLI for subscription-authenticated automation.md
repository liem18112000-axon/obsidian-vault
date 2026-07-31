---
ai_hash: 7d8d11efa013ff86
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-11
entities: []
source: Kepler/vinnstack code inspection, 2026-07-11
status: evergreen
tags:
- claude
- claude-code
- cli
- subprocess
- vinnstack
title: vinnstack spawns the local claude CLI for subscription-authenticated automation
type: howto
---

# vinnstack spawns the local claude CLI for subscription-authenticated automation

The vinnstack project (C:\Users\dvtliem\Kepler\vinnstack) reuses the operator's own Claude Pro/Max subscription for its agentic automation by shelling out to the locally-installed `claude` CLI as a child process, rather than calling the Anthropic API directly with a key.

Key implementation details (lib/ultracode/ultracodeRunner.ts, lib/interrogation/interrogationRunner.ts):
- Resolves the real `claude.exe` binary explicitly on Windows (an npm-global install only puts `.cmd`/`.ps1` wrapper scripts on PATH, and `child_process.spawn` can't execute those directly — it walks PATH for `claude.exe` first, then the nested `node_modules/@anthropic-ai/claude-code/bin/claude.exe`).
- Spawns with `stdio: ["pipe","pipe","pipe"]`, no shell (avoids shell-parsing/injection issues), and sends the prompt via **stdin**, never argv — because Windows caps total argv length (~32K chars) and a large prompt would hit `ENAMETOOLONG`.
- Invocation shape for a single non-interactive turn: `claude -p --model <model> --permission-mode dontAsk --allowedTools <tools...> --output-format json --append-system-prompt <instruction>`, prompt piped via stdin.
- `--output-format json` returns a JSON envelope with `result` (the answer text), `is_error` (bool), `num_turns`, `subtype` — parse `JSON.parse(stdout).result` for the actual text.
- Uses per-account `CLAUDE_CONFIG_DIR` (named by an opaque UUID, not email) so multiple operator identities keep separate subscription/login state on one machine.

This is a legitimate pattern **only** because vinnstack is a single-operator tool — the same person runs their own agentic tasks through their own subscription login. See [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]] for why this pattern does NOT transfer to an app that serves other people's requests, regardless of transport (network API vs. local CLI spawn).

## Related

- [[Claude subscription OAuth cannot power a third-party audience-facing app]]
- [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]]

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack AI calls are stateless headless claude CLI runs, not an agent runtime]]
- [[Claude Code's auto-mode permission classifier blocks building subscription-auth-for-third-parties even at smoke-test scale]]
- [[Vinnstack is local-only by design spawned-CLI login + local FS state + single-tenant]]
- [[Vinnstack per-request claude CLI spawn has a ~12s cold-start floor, model-independent]]
- [[Claude Code hooks fire for any spawned claude process, not just interactive sessions]]

%% ai-graph-end %%