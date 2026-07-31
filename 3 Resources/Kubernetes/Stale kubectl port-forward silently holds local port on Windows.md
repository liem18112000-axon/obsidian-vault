---
title: "Stale kubectl port-forward silently holds local port on Windows"
created: 2026-07-22
type: lesson
status: seedling
source: "session 2026-07-22"
tags: [kubernetes, windows, port-forward, gotcha]
---

# Stale kubectl port-forward silently holds local port on Windows

On Windows, a stale `kubectl.exe port-forward` keeps holding `127.0.0.1:<port>` after you have forgotten about it. A new forward on the same port fails with "Unable to listen on port ... bind: Only one usage of each socket address (protocol/network address/port) is normally permitted" — and worse, clients silently keep talking to whatever OLD pod the stale forward targets (wrong cluster / secondary → false auth failures, NotWritablePrimary).

Diagnose:

```powershell
netstat -ano | findstr :27017                      # find holder PID
(Get-CimInstance Win32_Process -Filter "ProcessId=<pid>").CommandLine   # see WHICH pod it forwards
taskkill /PID <pid> /F
```

The CommandLine check is the key step — it tells you whether the existing forward is actually the one you want or a leftover pointing at the wrong pod.

## Related

- [[NotWritablePrimary via port-forward means forward targets a secondary]]
