---
title: "Automate GitHub read-only deploy key for a server via gh + dedicated SSH key"
created: 2026-07-05
type: howto
status: seedling
source: "session 2026-07-05"
tags: [github, deploy-key, ssh, gh-cli, git, howto]
---

# Automate GitHub read-only deploy key for a server via gh + dedicated SSH key

To let a server clone a **private** GitHub repo unattended, use a repo-scoped **deploy key** rather than a personal token — it is read-only and bound to one repo, so it leaks the least.

**On the server** — generate a dedicated key and pin github.com to it:

```bash
ssh-keygen -t ed25519 -N "" -C "myapp-deploy@host" -f ~/.ssh/myapp_deploy
cat >> ~/.ssh/config <<CFG

Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/myapp_deploy
  IdentitiesOnly yes      # <-- only offer THIS key, dont walk every agent key
CFG
ssh-keyscan -t ed25519,rsa github.com >> ~/.ssh/known_hosts   # skip first-connect prompt
```

`IdentitiesOnly yes` matters: without it ssh offers every key in the agent/`~/.ssh`, which can trip GitHub rate limits or match the wrong key.

**Register the public key without opening the web UI** — the GitHub CLI can add it (needs `gh` logged in with ADMIN on the repo + `repo` scope):

```bash
gh repo deploy-key add ~/.ssh/myapp_deploy.pub -R OWNER/REPO -t "myapp@host"   # read-only by default; -w adds write
gh repo deploy-key list -R OWNER/REPO                                          # verify
```

**Then clone with the SSH URL** (`git@github.com:OWNER/REPO.git`) — the HTTPS URL would still prompt for a username. Verify with `git ls-remote git@github.com:OWNER/REPO.git HEAD`.

Surfaced deploying the AppsFlyer puller to `leocdp-obs1`: HTTPS clone failed with `could not read Username for https://github.com`; a deploy key + SSH URL fixed it. See also [[Git Bash /etc/hosts is not the Windows hosts file ssh reads]].
