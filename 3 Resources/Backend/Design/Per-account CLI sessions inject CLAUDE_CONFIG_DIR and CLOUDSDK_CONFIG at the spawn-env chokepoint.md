---
title: "Per-account CLI sessions: inject CLAUDE_CONFIG_DIR and CLOUDSDK_CONFIG at the spawn-env chokepoint"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04, vinnstack per-account CLI homes"
tags: [cli, multi-account, claude, gcloud, vinnstack]
---

# Per-account CLI sessions: inject CLAUDE_CONFIG_DIR and CLOUDSDK_CONFIG at the spawn-env chokepoint

Vendor CLI logins (claude auth login, gcloud auth login) are DISK sessions, not strings - you can't row-store them like API tokens. To make them per-user anyway, give each account its own CLI home via the CLIs' config-dir env vars:

    CLAUDE_CONFIG_DIR=<data>/accounts/<accountId>/claude
    CLOUDSDK_CONFIG=<data>/accounts/<accountId>/gcloud

Two design points that make it cheap:
1. **Inject at the spawn-env chokepoint.** If all CLI spawns already funnel through one env builder (agentEnv()/cleanEnv()), a single spread of `activeAccountCliEnv()` covers every call site - runner, chat, provider status/login buttons - with zero per-site changes.
2. **Absent account = empty object** = the CLIs fall back to their machine-level default homes, so the feature ships dark alongside an env-armed login gate.

Each account performs the interactive browser login ONCE; the session persists in its folder (on the pod: the /data PVC) across restarts. Pre-create the dirs (mkdir -p) before spawning. Same trick as multi-subscription Claude profile switchers (one CLAUDE_CONFIG_DIR per profile).

## Related

- [[Arm a new login gate by env presence so shipping auth cannot lock the operator out]]
