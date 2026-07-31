---
title: "Node spawn shell:false on Windows won't run .cmd/.ps1 wrappers (ENOENT)"
created: 2026-06-29
type: lesson
status: seedling
source: "Vinnstack debugging session 2026-06-29"
tags: [nodejs, windows, child_process, gotcha, security]
---

# Node spawn shell:false on Windows won't run .cmd/.ps1 wrappers (ENOENT)

Node's `child_process.spawn(cmd, args, {shell:false})` on **Windows** uses `CreateProcess`, which only launches real `.exe` images. It does **not** resolve `.cmd` / `.ps1` / `.bat` launcher scripts via `PATHEXT` — that resolution is a *shell* feature, not a `CreateProcess` one. So a command that runs fine when you type it in a terminal can throw `ENOENT` from `spawn(..., {shell:false})`.

This bites npm-global CLIs specifically. An `npm i -g` install puts only wrapper scripts on PATH — e.g. `claude`, `claude.cmd`, `claude.ps1` in `%APPDATA%\npm\` — while the real binary is nested at `<npm-prefix>\node_modules\@anthropic-ai\claude-code\bin\claude.exe`. There is **no bare `claude.exe` on PATH**, so `spawn("claude", {shell:false})` fails even though `claude` works in PowerShell/cmd (the shell resolves the `.cmd`).

**Fix without re-introducing injection:** resolve the real `.exe` yourself — walk each PATH dir for `claude.exe` directly (covers a native installer), then for the nested `node_modules\...\bin\claude.exe` (covers npm-global), and spawn that absolute path. Cache the result.

**Do NOT just set `{shell:true}`** to paper over it: that re-introduces shell parsing of every argument, so a prompt/arg containing shell metacharacters becomes an injection vector. Keeping `shell:false` + a resolved absolute exe path means args still pass as single argv elements (no parsing).

Symptom in the wild: the failed spawn was swallowed by a `try/catch` that silently fell back to a stub, so every chat message returned a canned offline reply with no error surfaced — the bug looked like "CLI not installed" when the CLI was installed and working.

Real example: Vinnstack `lib/ultracodeRunner.ts` — `resolveClaudeBin()` feeding `spawnClaude()`. The route emitted `{type:"error"}` on the ENOENT and the client fell through to a `simulate()` stub.

## Related
[[npm global install layout on Windows]] [[Shell injection via child_process]]

## Related

- [[npm global install layout on Windows]]
- [[Shell injection via child_process]]
