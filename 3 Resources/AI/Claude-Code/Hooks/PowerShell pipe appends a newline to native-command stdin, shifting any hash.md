---
title: "PowerShell pipe appends a newline to native-command stdin, shifting any hash"
created: 2026-06-02
type: lesson
status: seedling
source: "session 2026-06-02"
tags: [powershell, hashing, gotcha, testing]
---

# PowerShell pipe appends a newline to native-command stdin, shifting any hash

PowerShell appends a trailing newline when it pipes a string into a native command's stdin. So if you compute a hash (e.g. SHA1) from a file's exact bytes and then pipe that same file content to a process via `Get-Content -Raw | someExe`, the hash the process computes from *its* stdin will differ from yours â€” the process saw your bytes **plus** a newline.

This bites when verifying any stdin-hash protocol externally. Example: testing two hooks that coordinate via SHA1 of their stdin â€” pre-seeding a claim file with a hash you computed from the payload file will *never* match the hash the hook derives from its piped stdin, so the cooperation appears broken when it is not.

Workaround: don't recompute the hash externally. Let the process emit/persist its *own* hash on a first run (e.g. the claim file it writes), then reuse that true value for the test. Same stdin piped the same way is deterministic, so a second identical run matches.

Related: [[3 Resources/AI Tools/Claude Code/Hooks/Bash collapses backslashes before PowerShell stdin, breaking Windows-path JSON]], [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]].

## Related

- [[Bash collapses backslashes before PowerShell stdin]]
- [[breaking Windows-path JSON]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]

