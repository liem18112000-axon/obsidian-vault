---
title: "Relocating a hardcoded-path hook integration: self-locate or patch every reference site"
created: 2026-06-02
type: howto
status: seedling
source: "session 2026-06-02"
tags: [claude-code, hooks, skills, telegram, refactoring, technique, gotcha]
---

# Relocating a hardcoded-path hook integration: self-locate or patch every reference site

Hook files under `~/.claude/hooks/` are often OWNED by installed skills that hardcode `~/.claude/hooks/<name>`. Moving one naively breaks those owners, or the installer silently recreates the file at the old location (duplicates). The integration CAN still be moved - it just costs the work of fixing every reference site. Contrast: hooks wired ONLY through `settings.json` (which you control) are cheap to move.

**1. Make the runtime scripts self-locating (preferred).** A script that derives its siblings/state from its own directory survives the move untouched:
- Bash: `HERE="$(cd "$(dirname "$0")" && pwd)"`, then reference `$HERE/...`.
- Pass that dir to child processes via an env var (`export HOOKS_DIR="$HERE"`) and have embedded Python read `os.environ["HOOKS_DIR"]` instead of `os.path.expanduser("~/.claude/hooks/...")`. The Telegram poller already did this (`HOOKS_DIR` from `poller.sh`) and moved with zero edits; only `telegram-notify.sh`'s embedded Python had a hardcoded path (`telegram-input-map.jsonl`) to convert.

**2. Patch the sites that genuinely must know the absolute location** - they reference the integration from outside and cannot self-locate:
- `settings.json` hook command paths (the harness invokes them by absolute path).
- The toggle/management skill (`telegram-toggle/toggle.sh` had `HOOKS_DIR="$HOME/.claude/hooks"` -> `/telegram`).
- The installer skill (`telegram-hook-installation`) - its target dir AND its templates, so a re-install drops files in the new place instead of recreating the old top-level copies. Templates can drift stale from the live files; the target dir matters most.
- The OS autostart entry - a Windows Startup `.vbs` (in `%APPDATA%\...\Startup`, outside `.claude`) that launches the poller by absolute path.
- Pre-approved Bash permission patterns in `settings.local.json`, so maintenance commands stay allowlisted.
- `$PSScriptRoot`-derived shared state on the PowerShell side ([[PSScriptRoot-relative state breaks when a hook moves to a subfolder]]).

Checklist: grep the whole `.claude` tree (and the Startup folder) for `hooks/<prefix>-` BEFORE and AFTER, ignoring `file-history/` matches (immutable backups). Verify with `bash -n` on each script, `py_compile` on the daemon, re-validate `settings.json`, run the toggle's `status`, and smoke-test the daemon from the new location (it logs "started" before any network call) - then restore the prior on/off state. Same pattern applies to the `slack-*` cluster.

## Related

- [[PSScriptRoot-relative state breaks when a hook moves to a subfolder]]
- [[Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]
