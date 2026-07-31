---
title: "Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures"
created: 2026-07-13
type: lesson
status: seedling
source: "eArchive ops session 2026-07-13, tenant 45b05710 performance env"
tags: [kubernetes, kubectl, port-forward, gotcha]
---

# Stale kubectl port-forward on a reused local port causes silent wrong-target auth failures

When a `kubectl port-forward` process is left running on a local port (e.g. from an earlier background job that didn't clean up), a *new* script that just assumes the port is already correctly tunneled can silently connect through the **stale** tunnel instead of a fresh one — even after switching `kubectl config use-context` to a different cluster/project.

Symptom: `Authentication failed` even though the tenant credentials and cluster name are correct, because the local port is still forwarding to whatever pod the old process targeted (possibly a different cluster/project entirely from before a context switch).

Fix/diagnostic: don't assume an open local port means a fresh correct tunnel. Check who owns it first (on Windows: `Get-NetTCPConnection -LocalPort <port> | Get-Process`; on Linux/Mac: `lsof -i:<port>`), kill it, then re-establish the port-forward against the current context/cluster before retrying. A port-forward with no output visible on `cat` of its log file is not necessarily dead — it may just be quietly still running and healthy against the *wrong* target.

Related: [[Reuse an existing kubectl port-forward for ad-hoc mongo scripts]] — reusing a tunnel is fine when you know it's the right one; this note is about the failure mode when you don't verify that.

## Related

- [[Reuse an existing kubectl port-forward for ad-hoc mongo scripts]]
