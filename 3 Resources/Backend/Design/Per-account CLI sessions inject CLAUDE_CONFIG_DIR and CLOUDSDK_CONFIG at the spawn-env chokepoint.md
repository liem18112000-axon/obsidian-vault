---
ai_hash: d41596f7eeff752e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-04
entities: []
source: session 2026-07-04, vinnstack per-account CLI homes
status: seedling
tags:
- cli
- multi-account
- claude
- gcloud
- vinnstack
title: 'Per-account CLI sessions: inject CLAUDE_CONFIG_DIR and CLOUDSDK_CONFIG at
  the spawn-env chokepoint'
type: howto
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

%% ai-graph-start %%

**Related notes:**
- [[Anthropic has no third-party OAuth; in-app Claude login means driving the claude auth CLI]]
- [[Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry]]
- [[Claude Code headless auth setup-token prints a 1-year token, inject via CLAUDE_CODE_OAUTH_TOKEN]]
- [[Per-account credential store should only hold per-identity secrets]]
- [[Local-app provider sign-in drive the vendor CLI; Vertex is the exception (gcloud ADC + projectregion)]]

%% ai-graph-end %%