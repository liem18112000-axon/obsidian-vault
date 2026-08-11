---
ai_hash: 0db2abc4c75ff914
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-06
entities: []
source: leo-cdp-framework ci-cd.yml work 2026-06-06
status: seedling
tags:
- github-actions
- docker
- docker-compose
- ci
- gotcha
title: Shim legacy docker-compose v1 to docker compose v2 on GitHub runners
type: lesson
---

# Shim legacy docker-compose v1 to docker compose v2 on GitHub runners

A shell script that invokes the **legacy v1 `docker-compose`** binary can fail on GitHub `ubuntu-latest` runners, which ship Compose **v2** as the `docker compose` subcommand and may not provide the standalone `docker-compose` executable.

When you are constrained not to edit the script (e.g. it is a shared devops script that must stay identical for production hosts), drop in a shim instead of rewriting it:

```bash
if ! command -v docker-compose >/dev/null 2>&1; then
  printf "#!/usr/bin/env bash\nexec docker compose \"\$@\"\n" | sudo tee /usr/local/bin/docker-compose >/dev/null
  sudo chmod +x /usr/local/bin/docker-compose
fi
```

This keeps the original script byte-for-byte while making its `docker-compose ...` calls resolve to Compose v2. General pattern: prefer a PATH shim over editing a script you must keep as the single source of truth.

## Related

- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]

%% ai-graph-start %%

**Related notes:**
- [[LEO CDP CI provisions deps CI-natively, pinned to devops-script versions for parity]]
- [[CI build Docker image on every run, push only on non-PR]]
- [[Running the LEO CDP GHCR image needs mounted configs (image ships JARs only)]]
- [[Relocating docker-compose.yml renames the Compose project and orphans volumes]]
- [[GitHub Actions runners pick JDK from inherited JAVA_HOME, not PATH]]

%% ai-graph-end %%