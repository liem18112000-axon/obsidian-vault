---
ai_hash: b55f9b82a54eaec6
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-05
entities: []
source: session 2026-07-05
status: seedling
tags:
- github
- deploy-key
- ssh
- gh-cli
- git
- howto
title: Automate GitHub read-only deploy key for a server via gh + dedicated SSH key
type: howto
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

Surfaced deploying the AppsFlyer puller to `leocdp-obs1`: HTTPS clone failed with `could not read Username for https://github.com`; a deploy key + SSH URL fixed it. See also [[3 Resources/Tooling/Windows/Git-Bash/Git Bash etchosts is not the Windows hosts file ssh reads]].

%% ai-graph-start %%

**Related notes:**
- [[Pipe a GCP service-account key straight into a GitHub secret without leaking it]]
- [[GHCR-always plus Docker Hub-optional GitHub Actions publishing pattern]]
- [[Git Bash etchosts is not the Windows hosts file ssh reads]]
- [[Clone a Bitbucket repo with an app password without leaking it (inline credential helper)]]
- [[GitHub Actions 'secret is not set' usually means a name mismatch - verify with gh secret list]]

%% ai-graph-end %%