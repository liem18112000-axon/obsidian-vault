---
ai_hash: fea4d5c6011ccb4f
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-05
entities: []
source: session 2026-07-05
status: seedling
tags:
- git-bash
- msys
- windows
- bash
- permissions
- gotcha
title: Bash [[ -w ]] is unreliable on Windows/MSYS files guarded by ACL
type: lesson
---

# Bash [[ -w ]] is unreliable on Windows/MSYS files guarded by ACL

On Windows, Bash file-test operators like `[[ -w FILE ]]` check the **Unix-style permission bits** that MSYS/Git Bash synthesizes — not the actual **Windows ACL** that governs the write. So `[[ -w "$HOSTS_FILE" ]]` can return **true** for `C:\Windows\System32\drivers\etc\hosts` while a real append still dies with `Permission denied` (the ACL requires elevation).

**Consequence:** guarding a write with `if [[ ! -w "$f" ]]; then ...instructions...; fi` is a false safety net on Windows — control falls through to the write, which then fails with a raw OS error.

**Robust pattern — attempt the write, catch the failure, don`t pre-test:**

```bash
if printf "%s" "$block" >> "$HOSTS_FILE" 2>/dev/null; then
  echo "written"
else
  echo "not writable — run elevated" >&2
  exit 1
fi
```

When a redirection (`>>`) cannot open the file, Bash returns non-zero **without running the command**, so the `if` reliably detects it. This is portable (works on Linux/macOS too) and needs no `-w` probe.

Related: there is no `sudo` on Windows, so the usual `[[ -w f ]] || sudo cmd` fallback is doubly broken there. Surfaced in the LEO CDP @ VNG ssh helper scripts (`appsflyer-data-connector/scripts`). See also [[3 Resources/Tooling/Windows/Git-Bash/Git Bash etchosts is not the Windows hosts file ssh reads]].

## Related

- [[3 Resources/Tooling/Windows/Git-Bash/Git Bash etchosts is not the Windows hosts file ssh reads]]

%% ai-graph-start %%

**Related notes:**
- [[Git Bash etchosts is not the Windows hosts file ssh reads]]
- [[Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp]]

%% ai-graph-end %%