---
ai_hash: 809290f51da2f0c9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-13
entities: []
source: eArchive ops session 2026-07-13 (tenant 45b05710, performance env); sessions
  2026-07-22 / 2026-07-24
status: seedling
tags:
- kubernetes
- kubectl
- port-forward
- windows
- gotcha
title: Stale kubectl port-forward on a reused local port causes silent wrong-target
  auth failures
type: lesson
updated: 2026-07-31
---

# Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures

A forgotten `kubectl port-forward` keeps holding `127.0.0.1:<port>`. Two failure modes follow, and the second is the dangerous one:

1. **Loud:** a new forward on the same port fails with `Unable to listen on port … bind: Only one usage of each socket address (protocol/network address/port) is normally permitted` (Windows) — i.e. the tunnel you are trying to start may already exist and be working.
2. **Silent:** a script that just *assumes* the port is tunneled connects through the **stale** tunnel — even after `kubectl config use-context` switched cluster/project. Symptom: `Authentication failed` / `NotWritablePrimary` with correct credentials, because the port still points at the old pod (wrong cluster, or a replica-set secondary).

**Never assume an open local port means a fresh, correct tunnel — identify its owner's command line first.**

```powershell
Get-NetTCPConnection -LocalPort <port> -State Listen        # -> OwningProcess  (or: netstat -ano | findstr :<port>)
(Get-CimInstance Win32_Process -Filter "ProcessId=<pid>").CommandLine   # WHICH pod/ns it forwards — the key step
Test-NetConnection 127.0.0.1 -Port <port>                   # confirm it actually answers
Stop-Process -Id <pid> -Force                               # or: taskkill /PID <pid> /F
```

Linux/macOS: `lsof -i:<port>`. If the CommandLine shows the forward you wanted, just use it; only kill when you need to re-own the port, then re-establish against the current context. A port-forward whose log file looks empty is not necessarily dead — it may be quietly healthy against the *wrong* target.

## Related

- [[Reuse an existing kubectl port-forward for ad-hoc mongo scripts]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]
- [[NotWritablePrimary via port-forward means forward targets a secondary]]

%% ai-graph-start %%

**Related notes:**
- [[NotWritablePrimary via port-forward means forward targets a secondary]]
- [[earchive-seed-stale-27017-portforward-gotcha]]
- [[Verify kubectl context before GKE rollout - _context file can disagree]]
- [[Reuse an existing kubectl port-forward for ad-hoc mongo scripts]]
- [[Dev mongod pods have legacy mongo shell only, no mongosh]]

%% ai-graph-end %%