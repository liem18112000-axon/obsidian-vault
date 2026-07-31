---
title: "winget PATH update only applies to new shells, not the current session"
created: 2026-06-01
type: lesson
status: seedling
source: "session 2026-06-01"
tags: [windows, winget, batch, path, gotcha]
---

# winget PATH update only applies to new shells, not the current session

On Windows, `winget install <id>` updates the **PATH** environment variable only for **newly-spawned shells**. The cmd/batch (or PowerShell) session that ran the install already captured its PATH at launch, so a freshly-installed tool is still "not recognized" if you try to invoke it by bare name later in the same script.

**Why:** PATH changes from an installer propagate via the registry + a `WM_SETTINGCHANGE` broadcast; a process reads its environment once at startup and does not re-read it. So the install succeeds but the same-session call fails.

**Two workarounds:**
1. **Call by full install path** in the same session instead of relying on PATH. E.g. Ollama installs to `%LOCALAPPDATA%\Programs\Ollama\ollama.exe` — invoke that directly right after install.
2. **Detect-and-bail:** test whether the tool resolves on PATH (`where /q <tool>`); if not, tell the user to close the window, open a **new** terminal, and re-run the script. Good for tools whose install path varies (e.g. Python via the `py` launcher).

Surfaced writing an `install.cmd` that installs Ollama and Python 3.12 via winget, then immediately uses them in the same run — Ollama took workaround #1, Python took #2.

Related: [[PATH environment variable]]

## Related

- [[PATH environment variable]]
