---
title: "Vinnstack is local-only by design: spawned-CLI login + local FS state + single-tenant"
created: 2026-07-01
type: argument
status: seedling
source: "session 2026-07-01 (doc/why-local-only.md)"
tags: [vinnstack, architecture, deployment, local-first, design-decision]
---

# Vinnstack is local-only by design: spawned-CLI login + local FS state + single-tenant

Vinnstack is an "Agentic Operation System" deliberately built as a **single-operator local desktop app** (Electron / next dev on localhost:3001), NOT a web service. It cannot be dropped on a serverless/web host because its whole model is local:

1. It **spawns the local `claude` CLI** as a child process, authenticated by the operator's own Claude Code login/subscription — it strips ANTHROPIC_* env so it can't fall back to an API key (lib/ultracodeRunner.ts spawnClaude/resolveClaudeBin/agentEnv). No CLI + no interactive login on a web host.
2. The **local filesystem is the database**: Obsidian vault (long-term memory + Skills + interrogation JSON), ~/.agentic-os/config.json (secrets, ACL-locked to the OS user), scratch, Graphify graphs. config.ts: 'installs per machine … secrets NEVER leave the machine.'
3. The spawned agent drives **local dev tooling** (gcloud/kubectl/git/Bitbucket) bound to the machine's creds/VPN/PATH.
4. Requests run **minutes-long synchronously** (~5-min headless claude; maxDuration 540). Serverless caps request duration.
5. **Single-tenant** — one operator's creds/vault/login; no multi-tenant isolation.

Web-deployable would be a different product: API-key auth instead of CLI login, per-user datastore for all FS state, multi-tenant secret isolation, async jobs for the long runs, and brokered least-privilege dev tooling. Documented in repo doc/why-local-only.md.
