---
ai_hash: 0d064d066def9f97
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-29
entities: []
status: seedling
tags:
- nodejs
- windows
- child-process
- child_process
- gotcha
- security
title: Node spawn shell:false on Windows won't run .cmd/.ps1 wrappers (ENOENT)
type: gotcha
---

# Node spawn shell:false on Windows won't run .cmd/.ps1 wrappers (ENOENT)

On Windows, `spawn`/`execFile`/`execFileSync` without `shell:true` go through `CreateProcess`, which launches only real `.exe` images. It does **not** resolve `.cmd` / `.bat` / `.ps1` launcher scripts via `PATHEXT` — that is a *shell* feature. So a command that works when typed in a terminal throws `Error: spawnSync <cmd> ENOENT` from Node. (`exec()` is unaffected; it always uses a shell.)

This bites CLIs that ship as batch shims: `gcloud`, `npm`, and every `npm i -g` install — e.g. `%APPDATA%\npm\claude`, `claude.cmd`, `claude.ps1` with the real binary buried at `<npm-prefix>\node_modules\@anthropic-ai\claude-code\bin\claude.exe`. There is no bare `claude.exe` on PATH. A real `.exe` target works fine with plain `execFile`.

## Two fixes — pick by trust level of the args

1. **`{ shell: true }`** — simplest; cross-platform safe (on POSIX it just runs via `/bin/sh -c`), so it need not be OS-conditional. **Only when every argument is internally constructed.** With `shell:true` the argv array is interpolated into a command line, so any untrusted/attacker-controlled argument is a shell-injection vector.
2. **Resolve the real `.exe` yourself and keep `shell:false`** — walk each PATH dir for `<tool>.exe`, then for the nested `node_modules\...\bin\<tool>.exe`, and spawn that absolute path; cache the result. Args stay single argv elements, no shell parsing. Required whenever args carry user input (e.g. a chat prompt).

**Why it hides:** the failed spawn is usually swallowed by a `try/catch` that falls back to a stub, so it looks like "CLI not installed" rather than a spawn bug. Real case: Vinnstack `lib/ultracodeRunner.ts` `resolveClaudeBin()` / `spawnClaude()` — the route emitted `{type:"error"}` on the ENOENT and the client silently served a canned `simulate()` reply. Also hit in an Electron main-process `gcloud` ADC check before starting a `cloud-sql-proxy` sidecar, and in the `earchive-perf-trace` tool's `execFileSync('gcloud', args)`.

## Related

- [[spawn python ENOENT on Windows — resolve a real interpreter, not the Store alias]]
- [[npm global install layout on Windows]]
- [[Shell injection via child_process]]
- [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]]

%% ai-graph-start %%

**Related notes:**
- [[A silent canned fallback masks real failures — surface the underlying error]]
- [[spawn python ENOENT on Windows — resolve a real interpreter, not the Store alias]]
- [[ELECTRON_RUN_AS_NODE silently makes electron.exe behave as plain Node]]
- [[Never cache a negative fallback in the same slot as a resolved value]]
- [[Pass LLM prompts to spawned CLIs via stdin - Windows argv caps at 32K (ENAMETOOLONG)]]

%% ai-graph-end %%