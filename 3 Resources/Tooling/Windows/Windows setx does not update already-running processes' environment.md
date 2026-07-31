---
ai_hash: 89df3fc679a5a081
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07
status: seedling
tags:
- windows
- environment-variables
- setx
- gotcha
title: Windows setx does not update already-running processes' environment
type: lesson
---

# Windows setx does not update already-running processes' environment

`setx NAME value` persists an env var to the registry (user or machine scope) for **future** processes only. It never touches the environment of a running process — including the shell you ran it from. Windows broadcasts `WM_SETTINGCHANGE`, but terminals, IDEs, and long-running dev servers generally ignore it; a process reads its environment once at launch. Restart the process (or close and reopen the terminal/IDE) before `process.env.NAME` reflects the new value.

**The false-confidence trap:** verifying the mirror with `[Environment]::GetEnvironmentVariable(name, "User")` shows the new value immediately, while the already-running app still sees the old/absent one. Hit in Vinnstack, where `lib/config.ts` `saveConfig()` mirrors onboarding credentials via `setx`: the OS-level var and the DB row both existed, but a server-side gate reading `process.env.VINNSTACK_EMAIL` inside an earlier-started `next dev` kept failing.

**Reliable fix:** for a value a specific dev/build process needs, write it into that process's `.env` (re-read on every start) instead of relying on the OS-level mirror to propagate.

## Related

- [[winget PATH update only applies to new shells, not the current session]]

%% ai-graph-start %%

**Related notes:**
- [[Config read into a module-level const applies only on next process launch]]
- [[Node.js process.env is case-insensitive on Windows]]
- [[winget PATH update only applies to new shells, not the current session]]
- [[Never cache a negative fallback in the same slot as a resolved value]]
- [[Open-and-degrade beats hard-quit let a desktop app start without its optional DB (Vinnstack clean-Win fix)]]

%% ai-graph-end %%