---
title: "Shim legacy docker-compose v1 to docker compose v2 on GitHub runners"
created: 2026-06-06
type: lesson
status: seedling
source: "leo-cdp-framework ci-cd.yml work 2026-06-06"
tags: [github-actions, docker, docker-compose, ci, gotcha]
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
