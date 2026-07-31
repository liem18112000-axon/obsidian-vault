---
title: "Split intermixed single-file changes into two commits via backup and intermediate edit"
created: 2026-06-04
type: howto
status: seedling
source: "session 2026-06-04"
tags: [git, windows, powershell, technique]
---

# Split intermixed single-file changes into two commits via backup and intermediate edit

To split intermixed changes inside ONE file into two clean commits when interactive staging (`git add -p` / `-i`) is unavailable (e.g. non-interactive PowerShell on Windows):

1. Copy the full modified file aside: `Copy-Item File.java $env:TEMP\File.full.java`
2. `git reset` (unstage everything)
3. Edit the working file down to an **intermediate version** containing only commit 1's hunks — revert every other hunk back to HEAD's exact lines.
4. **Compile-check before committing** — reverting a hunk can strand leftovers from the kept hunks (e.g. a stray `return true;` in a method whose signature you reverted to `void`).
5. `git add File.java` → commit 1.
6. Copy the full file back, `git add -A`, commit 2. The second diff falls out automatically.

The intermediate version is throwaway — final tree is byte-identical to the original full version, so prior test runs remain valid for the end state (but commit 1 itself needs its own compile check since it will live alone, e.g. as a cherry-pick).

Related: [[LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master]]

## Related

- [[LUZ-155107 shipped as two commits so the inheritedSecurityClassCode fix can cherry-pick to earchive-master]]
