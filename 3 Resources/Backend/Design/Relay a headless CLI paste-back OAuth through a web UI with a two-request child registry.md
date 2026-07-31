---
title: "Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry"
created: 2026-07-04
type: howto
status: seedling
source: "session 2026-07-04, vinnstack CLI login relay"
tags: [oauth, cli, headless, nextjs, vinnstack]
---

# Relay a headless CLI paste-back OAuth through a web UI with a two-request child registry

To let an operator complete a vendor CLI's browser-OAuth from a web UI when the CLI runs on a REMOTE headless pod (its localhost is unreachable from the operator's browser): only a PASTE-BACK code flow is relayable (a pure loopback localhost:PORT redirect is not — the redirect can't reach the pod). gcloud has it (`--no-launch-browser`); many CLIs don't expose one.

Mechanism — two HTTP calls straddle ONE long-lived child:
1. start: `spawn(cli, [...], {stdio:['pipe','pipe','pipe'], env})`, watch stdout+stderr for the first URL, hold the child in an in-module `Map` keyed by session id, return the URL. If the child EXITS before any URL, or no URL within ~60s, it's a failure (likely loopback-only / bad flag).
2. submitCode: look the child up, `child.stdin.write(code+"\n")`, await close; exit 0 = success.

Requirements/gotchas:
- Only safe on a SINGLE-replica server (in-memory child registry isn't shared across replicas) — a StatefulSet with replicas:1 qualifies; a scaled Deployment does not (needs sticky sessions or external state).
- TTL-sweep the registry and kill on cancel, or an abandoned flow leaks a process.
- Spawn with the per-account CLI-home env so the resulting session lands in the right config dir.
- Alternative that sidesteps all of it: TOKEN INJECTION — mint a long-lived token elsewhere and drop it into the CLI's config dir/env; no interactive flow in the pod at all. Prefer it when the CLI supports it.

## Related

- [[Per-account CLI sessions: inject CLAUDE_CONFIG_DIR and CLOUDSDK_CONFIG at the spawn-env chokepoint]]
