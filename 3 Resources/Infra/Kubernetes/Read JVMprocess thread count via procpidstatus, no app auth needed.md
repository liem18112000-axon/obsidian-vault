---
ai_hash: 64c0b645a7462c01
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-23
entities: []
source: session 2026-07-23, luz-docs resource-specs investigation
tags:
- kubernetes
- linux
- monitoring
- jvm
title: Read JVM/process thread count via /proc/pid/status, no app auth needed
type: technique
---

# Read JVM/process thread count via /proc/pid/status, no app auth needed

To count a process's OS threads inside a Kubernetes pod without needing any application-level auth or introspection tooling (no jstack/jcmd needed for Java, no serverStatus auth needed for Mongo), just read the kernel's own process status file:

`kubectl exec <pod> -c <container> -- cat /proc/<pid>/status | grep Threads:`

This works identically for ANY process -- a JVM (Wildfly/OpenJDK), mongod, anything -- because it's the kernel's own bookkeeping, not something the application has to expose. For containers where PID 1 IS the actual target process (many minimal images run the app directly as PID 1, e.g. a mongod container with no wrapper shell), you don't even need to find the PID first. For containers with a wrapper shell (PID 1 = /bin/sh -c ..., actual app spawned as a child), grep `ps -eo pid,comm` for the process name first.

Useful when the 'proper' introspection route needs credentials you don't have or shouldn't fetch (e.g. an app requires DB authentication for a stats command, and reading the DB's admin-credentials Secret is rightly gated) -- /proc gives you a real, auth-free signal (thread count) even if you can't get the full picture (per-operation connection stats etc).

%% ai-graph-start %%

**Related notes:**
- [[luz-docs performance JVM thread metrics endpoint]]
- [[Count _shard docs per tenant via in-pod Percona mongo shell on dev]]
- [[Git Bash mangles absolute POSIX paths meant for a remote kubectl exec target]]

%% ai-graph-end %%