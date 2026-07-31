---
title: "Windows setx does not update already-running processes' environment"
created: 2026-07-07
type: lesson
status: seedling
source: "Vinnstack session 2026-07-07"
tags: [windows, environment-variables, setx, gotcha]
---

# Windows setx does not update already-running processes' environment

On Windows, `setx NAME value` persists an environment variable to the registry (user or machine scope) for FUTURE processes, but it never touches the environment of processes that are already running — including the shell/terminal you ran `setx` from. Windows broadcasts a `WM_SETTINGCHANGE` message on the change, but most already-open terminals, IDEs, and long-running dev servers do not react to it; you need to fully close and reopen the terminal (sometimes the IDE itself) — or just restart the process — before `process.env.NAME` reflects the new value there.

This is a common trap for a "mirror this captured credential into the system environment for convenience" feature: verifying the mirror worked by checking `[Environment]::GetEnvironmentVariable(name, "User")` will show the new value immediately, giving false confidence, while the actual running application (e.g. a `next dev` server started earlier) still has the OLD (or absent) value in its own `process.env`, because that value was captured from the environment at THAT process's launch time and never gets refreshed.

Concretely hit in Vinnstack: `lib/config.ts`'s `saveConfig()` mirrors onboarding-captured credentials via `setx` (see [[Vinnstack interrogationStore full-aggregate rewrite loses concurrent updates to the same epic]]'s sibling feature). The operator env var got set correctly at the OS level, but the already-running `next dev` process never saw it, so a server-side gate that specifically checks `process.env.VINNSTACK_EMAIL` (not a merged/fallback config value) kept failing even though the OS-level env var and the matching DB row both existed. The reliable fix for a value a specific dev/build process needs is to put it directly in that process' `.env` file (reloaded fresh on every start) rather than relying on the OS-level mirror to have propagated.
