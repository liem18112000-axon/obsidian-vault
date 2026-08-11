---
ai_hash: 86e2b7403aa0d5a3
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-03
entities: []
source: session 2026-07-03
status: seedling
tags:
- powershell
- windows
- execution-policy
- gotcha
title: Run local unsigned PowerShell scripts under AllSigned via CurrentUser RemoteSigned
type: howto
---

# Run local unsigned PowerShell scripts under AllSigned via CurrentUser RemoteSigned

A machine-wide **LocalMachine** execution policy of `AllSigned` blocks *every* `.ps1` — including scripts you authored locally — with `File ... is not digitally signed. You cannot run this script`. You do not need admin or a code-signing cert to get past it: set a **CurrentUser**-scoped policy, which overrides LocalMachine.

```powershell
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
```

`RemoteSigned` runs local scripts freely (no signature needed) but still requires a signature on scripts carrying the *mark-of-the-web* (downloaded from the internet). Because scope precedence puts CurrentUser above LocalMachine, this quietly lifts the `AllSigned` block for your own account without touching the machine setting.

One-off alternative that changes no policy at all — bypass for a single invocation:

```powershell
powershell -ExecutionPolicy Bypass -File .\script.ps1
```

Discovered running Vinnstack repo scripts (`scripts/connect-cloud-db.ps1`, `scripts/setup-local-db.ps1`) on Windows.

## Related
[[PowerShell execution policy scope precedence and the benign override warning]]

## Related

- [[PowerShell execution policy scope precedence and the benign override warning]]

%% ai-graph-start %%

**Related notes:**
- [[PowerShell execution policy scope precedence and the benign override warning]]

%% ai-graph-end %%