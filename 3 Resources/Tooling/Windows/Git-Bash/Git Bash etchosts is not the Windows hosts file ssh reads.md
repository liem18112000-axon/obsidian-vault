---
ai_hash: 311d55b4fb4df6f9
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
- hosts
- ssh
- gotcha
title: Git Bash /etc/hosts is not the Windows hosts file ssh reads
type: gotcha
---

# Git Bash /etc/hosts is not the Windows hosts file ssh reads

Under Git Bash / MSYS on Windows there are **two** files named "hosts", and they are not the same file:

- **MSYS `/etc/hosts`** — lives inside the Git install (e.g. `C:\Program Files\Git\etc\hosts`). Only MSYS-internal name lookups see it.
- **Windows hosts file** — `C:\Windows\System32\drivers\etc\hosts`, addressed as `/c/Windows/System32/drivers/etc/hosts` in MSYS path form. This is what the **Windows resolver** reads, so `ssh`, `ping`, and every native Windows tool consult *this* one — never the MSYS `/etc/hosts`.

**The trap:** a shell script that greps/appends `/etc/hosts` will happily report `leocdp-obs1 already mapped` while `ssh leocdp-obs1` still dies with `Could not resolve hostname ... Name or service not known`. The entry is real — just in the file nothing outside MSYS looks at.

**Fix:** pick the hosts file by platform.

```bash
case "$(uname -s)" in
  MINGW*|MSYS*|CYGWIN*) HOSTS_FILE="/c/Windows/System32/drivers/etc/hosts" ;;
  *)                    HOSTS_FILE="/etc/hosts" ;;
esac
```

**Two follow-on gotchas:** writing to the Windows hosts file needs **Administrator** (run an elevated PowerShell + `Add-Content`), and there is **no `sudo`** on Windows — the usual `[[ -w file ]] || sudo` fallback silently does nothing useful.

Surfaced building the LEO CDP @ VNG ssh helper scripts in `appsflyer-data-connector/scripts`.

%% ai-graph-start %%

**Related notes:**
- [[Bash [[ -w ]] is unreliable on WindowsMSYS files guarded by ACL]]
- [[Automate GitHub read-only deploy key for a server via gh + dedicated SSH key]]
- [[Windows Python resolves a leading-slash path to C-colon-tmp, not Git Bash tmp]]

%% ai-graph-end %%