---
title: "bind only-one-usage on port-forward means a previous forward already owns the port"
created: 2026-07-24
type: lesson
status: seedling
source: "session 2026-07-24"
tags: [kubectl, port-forward, windows, gotcha]
---

# bind only-one-usage on port-forward means a previous forward already owns the port

kubectl port-forward failing on Windows with 'bind: Only one usage of each socket address (protocol/network address/port) is normally permitted' means the local port is already held — very often by a PREVIOUS kubectl port-forward still running in another terminal, i.e. the thing you are trying to start already exists and may be working.

Diagnose before killing: Get-NetTCPConnection -LocalPort <port> -State Listen → OwningProcess, then (Get-CimInstance Win32_Process -Filter "ProcessId=<pid>").CommandLine shows exactly which forward it is, and Test-NetConnection 127.0.0.1 -Port <port> confirms it works. If it is the right forward, just use it; only Stop-Process -Id <pid> -Force when you need to re-own it.

## Related
- [[namespaces dev not found on port-forward means wrong kubectl context]]
