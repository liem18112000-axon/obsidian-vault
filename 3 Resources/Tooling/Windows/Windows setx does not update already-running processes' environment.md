---
title: "Windows setx does not update already-running processes' environment"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [windows, environment-variables, setx, gotcha]
---

# Windows setx does not update already-running processes' environment

`setx NAME value` persists an env var to the registry (user or machine scope) for **future** processes only. It never touches the environment of a running process — including the shell you ran it from. Windows broadcasts `WM_SETTINGCHANGE`, but terminals, IDEs, and long-running dev servers generally ignore it; a process reads its environment once at launch. Restart the process (or close and reopen the terminal/IDE) before `process.env.NAME` reflects the new value.

**The false-confidence trap:** verifying the mirror with `[Environment]::GetEnvironmentVariable(name, "User")` shows the new value immediately, while the already-running app still sees the old/absent one. Hit in Vinnstack, where `lib/config.ts` `saveConfig()` mirrors onboarding credentials via `setx`: the OS-level var and the DB row both existed, but a server-side gate reading `process.env.VINNSTACK_EMAIL` inside an earlier-started `next dev` kept failing.

**Reliable fix:** for a value a specific dev/build process needs, write it into that process's `.env` (re-read on every start) instead of relying on the OS-level mirror to propagate.

## Related

- [[winget PATH update only applies to new shells, not the current session]]
