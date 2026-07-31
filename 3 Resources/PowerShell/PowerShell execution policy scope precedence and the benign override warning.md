---
title: "PowerShell execution policy scope precedence and the benign override warning"
created: 2026-07-03
type: lesson
status: seedling
source: "session 2026-07-03"
tags: [powershell, windows, execution-policy, gotcha]
---

# PowerShell execution policy scope precedence and the benign override warning

PowerShell resolves the effective execution policy by scope, in this precedence (highest wins): **MachinePolicy > UserPolicy > Process > CurrentUser > LocalMachine**. A more-specific scope silently overrides a broader one.

The gotcha: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` can print *"Windows PowerShell updated your execution policy successfully, but the setting is overridden by a policy defined at a more specific scope ... your shell will retain its current effective execution policy of Bypass."* This looks like a failure but is **benign** — it happens when the *current* shell was launched with `-ExecutionPolicy Bypass`, which sets the `Process` scope, and `Process` outranks `CurrentUser`. The CurrentUser value **is** persisted; it just does not affect *this* already-running process. A newly opened interactive PowerShell (with no Process override) picks it up.

Verify actual per-scope values, not just the effective one:

```powershell
Get-ExecutionPolicy -List
```

## Related
[[Run local unsigned PowerShell scripts under AllSigned via CurrentUser RemoteSigned]]

## Related

- [[Run local unsigned PowerShell scripts under AllSigned via CurrentUser RemoteSigned]]
