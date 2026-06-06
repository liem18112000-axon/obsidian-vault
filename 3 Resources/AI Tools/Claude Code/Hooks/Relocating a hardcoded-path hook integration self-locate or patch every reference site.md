---
title: "Relocating a hardcoded-path hook integration: self-locate or patch every reference site"
created: 2026-06-02
type: howto
status: seedling
source: "session 2026-06-02"
tags: [claude-code, hooks, telegram, refactoring, technique]
---

# Relocating a hardcoded-path hook integration: self-locate or patch every reference site

An integration whose files are referenced by hardcoded `~/.claude/hooks/<name>` paths (e.g. the Telegram bridge) CAN be moved into a subfolder â€” but only if you fix every reference site. Two complementary techniques:

**1. Make the runtime scripts self-locating (preferred).** A script that derives its siblings/state from its own directory survives the move untouched:
- Bash: `HERE="$(cd "$(dirname "$0")" && pwd)"`, then reference `$HERE/...`.
- Pass that dir to child processes via an env var (`export HOOKS_DIR="$HERE"`) and have embedded Python read `os.environ["HOOKS_DIR"]` instead of `os.path.expanduser("~/.claude/hooks/...")`. The Telegram poller already did this (`HOOKS_DIR` from `poller.sh`), so it moved with zero edits; only `telegram-notify.sh`'s embedded Python had a hardcoded path that needed converting.

**2. Patch the sites that genuinely must know the absolute location.** These can't self-locate because they reference the integration from outside:
- `settings.json` hook command paths (the harness invokes them by absolute path).
- The toggle/management skill (`toggle.sh` had `HOOKS_DIR="$HOME/.claude/hooks"` â†’ `/telegram`).
- The installer skill â€” its target dir AND its templates, so a future re-install drops files in the new place (else it recreates the old top-level copies). Note: templates can drift stale from the live files; patching the target dir matters most.
- The OS autostart entry â€” a Windows Startup `.vbs` (in `%APPDATA%\...\Startup`, outside `.claude`) that launches the poller by absolute path.
- Pre-approved Bash permission patterns in `settings.local.json` (so maintenance commands stay allowlisted).

Checklist to find them all: grep the whole `.claude` tree (and the Startup folder) for `hooks/<prefix>-` BEFORE and AFTER, treating `file-history/` matches as immutable backups to ignore. Verify with: `bash -n` each script, `py_compile` the daemon, re-validate `settings.json`, run the toggle's `status`, and do a brief live smoke test of the daemon from the new location (it logs "started" before any network call) â€” then restore prior on/off state.

Related: [[PSScriptRoot-relative state breaks when a hook moves to a subfolder]] (the PowerShell-side equivalent), [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]].

## Related

- [[3 Resources/AI Tools/Claude Code/Hooks/Hook files referenced by external skills via hardcoded paths cannot be relocated]]
- [[PSScriptRoot-relative state breaks when a hook moves to a subfolder]]

