---
title: "Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON"
created: 2026-06-02
type: lesson
status: seedling
source: "session 2026-06-02"
tags: [powershell, bash, windows, json, gotcha, testing]
---

# Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON

When feeding a JSON payload that contains Windows paths to a PowerShell stdin hook from the Bash tool, a literal `\` (escaped backslash) in a single-quoted bash string gets collapsed to a single `\` before it reaches PowerShell â€” while `\n` survives untouched. So `"C:\proj\app"` arrives as `"C:\proj\app"`, which is **invalid JSON** (`\p` is an unrecognized escape).

`ConvertFrom-Json` then throws. If the hook has `trap { exit 0 }` (the standard safety wrapper), the failure is swallowed silently â€” the hook produces no output and *looks like it simply chose not to fire*, masking the real cause. This cost real debugging time.

Workaround: do not build the payload as a bash string at all. Write it to a file with exact bytes (e.g. via an editor/Write tool) and pipe it with `Get-Content "<file>" -Raw | powershell ... hook.ps1`. The file content is delivered verbatim, so the JSON stays valid.

Related: [[3 Resources/AI Tools/Claude Code/Hooks/PowerShell pipe appends a newline to native-command stdin, shifting any hash]], [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]].

## Related

- [[PowerShell pipe appends a newline to native-command stdin]]
- [[shifting any hash]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]

