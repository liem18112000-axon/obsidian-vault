---
title: "Bash [[ -w ]] is unreliable on Windows/MSYS files guarded by ACL"
created: 2026-07-05
type: lesson
status: seedling
source: "session 2026-07-05"
tags: [git-bash, msys, windows, bash, permissions, gotcha]
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

Related: there is no `sudo` on Windows, so the usual `[[ -w f ]] || sudo cmd` fallback is doubly broken there. Surfaced in the LEO CDP @ VNG ssh helper scripts (`appsflyer-data-connector/scripts`). See also [[Git Bash /etc/hosts is not the Windows hosts file ssh reads]].

## Related

- [[Git Bash /etc/hosts is not the Windows hosts file ssh reads]]
