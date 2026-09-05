---
title: "MSYS /c/ paths passed to native Windows node become C:\c\ (ENOENT)"
created: 2026-08-25
type: lesson
status: seedling
source: "session 2026-08-25"
tags: [windows, nodejs, msys, git-bash, gotcha]
---

# MSYS /c/ paths passed to native Windows node become C:\c\ (ENOENT)

On Windows, native `node` does **not** understand MSYS/Git-Bash paths like `/c/Users/...`. Passing one to `node -e` (e.g. `require("fs").readFileSync("/c/Users/x")`) makes Node resolve it relative to the current drive root, turning it into `C:\c\Users\x` → `ENOENT`.

Bash builtins (`cd`, `cat`, `ls`) accept `/c/...` because MSYS translates them, but that translation does **not** apply to arguments handed to a native `.exe` such as `node`.

**Fix:** use forward-slash *Windows* paths (`C:/Users/...` — Node accepts forward slashes on Windows), or avoid the shell-quoting entirely by using the Write/Read tools for file I/O in scripts.

## Related

- [[Excalidraw JSON generator ghost-text: filtering a node's rectangle by id leaves its text elements behind]]
