---
title: "Git Bash mangles absolute POSIX paths meant for a remote kubectl exec target"
created: 2026-07-23
type: lesson
source: "session 2026-07-23, luz-docs resource-specs investigation"
tags: [gitbash, windows, kubernetes, gotcha]
---

# Git Bash mangles absolute POSIX paths meant for a remote kubectl exec target

Git Bash on Windows automatically rewrites any command-line argument that LOOKS like an absolute POSIX path (starts with `/`) into a Windows-style path, BEFORE handing it to the program being invoked. This is usually helpful (e.g. turning `/c/Users/...` into `C:\Users\...` for a native Windows tool) -- but it silently breaks any command where that argument is meant to be interpreted by something OTHER than the local Windows filesystem, most commonly `kubectl exec <pod> -- /absolute/path/on/the/remote/container`. The path gets mangled into something like `C:/Program Files/Git/opt/java/openjdk/bin/jcmd`, which obviously doesn't exist -- 'no such file or directory' inside the remote container, even though the path is completely correct there.

Fix: set `MSYS_NO_PATHCONV=1` in the environment for that command, which disables Git Bash's automatic path conversion entirely. Only needed when the absolute-path argument targets a REMOTE environment (another container, a different filesystem) rather than the local machine.

Where this bit specifically: `kubectl exec pod -c container -- /opt/java/openjdk/bin/jcmd 148 Thread.print` from within Git Bash. Worked instantly once MSYS_NO_PATHCONV=1 was set. See [[Read JVM/process thread count via /proc/pid/status, no app auth needed]] for the investigation this came up in (ended up not needing the absolute-path jcmd call at all once /proc/<pid>/status covered the same need).

## Related

- [[Read JVM/process thread count via /proc/pid/status]]
- [[no app auth needed]]
