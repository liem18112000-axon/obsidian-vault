---
title: "Hook files referenced by external skills via hardcoded paths cannot be relocated"
created: 2026-06-02
type: lesson
status: seedling
source: "session 2026-06-02"
tags: [claude-code, hooks, skills, refactoring, gotcha]
---

# Hook files referenced by external skills via hardcoded paths cannot be relocated

Before relocating any file under `~/.claude/hooks/`, grep the whole `.claude` tree (especially `skills/`) for references to its path â€” some hook files are OWNED by installed skills that hardcode `~/.claude/hooks/<name>` and will break or silently recreate the file at the old location if you move it.

> [!update] They CAN be relocated â€” it just costs more. A later session moved the whole `telegram-*` cluster into `hooks/telegram/` successfully by self-locating the scripts and patching every reference site. See [[3 Resources/AI Tools/Claude Code/Hooks/Relocating a hardcoded-path hook integration self-locate or patch every reference site]]. The point below stands as the *cost* you take on, not a hard blocker.

Concrete case: the `telegram-*` cluster has many hidden owners â€” moving it naively breaks them:
- `telegram-toggle/toggle.sh` hardcodes `HOOKS_DIR="$HOME/.claude/hooks"` and operates on `telegram-notify.disabled` plus relaunches `~/.claude/hooks/telegram-input-poller.sh`.
- `telegram-hook-installation` drops files at `~/.claude/hooks/telegram-*` and would recreate them there on its next run (causing duplicates).
- The poller and the embedded Python in `telegram-notify.sh` use absolute `~/.claude/hooks/telegram-input-map.jsonl` (not `$HERE`-relative), and a Windows Startup `.vbs` autostarts the poller by absolute path.

Contrast: hooks wired ONLY through `settings.json` (which you control) are safe to move into subfolders â€” just update the absolute command paths in `settings.json` and fix any `$PSScriptRoot`-derived shared state ([[PSScriptRoot-relative state breaks when a hook moves to a subfolder]]). The same pattern applies to the `slack-*` cluster.

Related: [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]].

## Related

- [[PSScriptRoot-relative state breaks when a hook moves to a subfolder]]
- [[3 Resources/AI Tools/Claude Code/Hooks/Cooperating PostToolUse hooks via a shared per-event SHA1 claim file]]

