---
title: "A process-global active account leaks identity across concurrent requests in a single-process server"
created: 2026-07-06
type: gotcha
status: seedling
source: "vinnstack multi-account review 2026-07-06"
tags: [multi-tenancy, concurrency, async-local-storage, identity, vinnstack]
---

# A process-global active account leaks identity across concurrent requests in a single-process server

Storing the "current user / active account / tenant" in a MODULE-LEVEL mutable global (let active = ...; setActive() mutates it) works for a single-operator tool but silently breaks the moment two users hit the same process concurrently. It's last-writer-wins: request B's setActive() overwrites the global while request A is still running, so any code A runs afterward (spawning a subprocess, reading credentials, choosing a config dir) picks up B's identity.

Vinnstack case: `active` in accountCredentials.ts is read by activeAccountCliEnv() to pick CLAUDE_CONFIG_DIR + CLAUDE_CODE_OAUTH_TOKEN and by getConfig() to pick Jira/Bitbucket creds. On a single-replica pod (one Node process, all users), Alvin's generation could spawn `claude` under Liem's token/config because Liem's request flipped the global between the route start and the spawn. On-disk per-account dirs were correctly isolated; the SELECTION of which one wasn't request-scoped — that's the race.

Tell: log lines showing the "active X set" value flipping between users within seconds; intermittent "used the wrong account" behavior that vanishes with one user.

Fix: make the identity REQUEST-SCOPED. In Node, AsyncLocalStorage is the low-churn fix — set the account in an ALS store at request entry; the accessor reads ALS instead of a module global, so every downstream call sees the requesting user's identity without threading a param through every function. (Threading the identity explicitly is the more invasive but equally correct alternative.) A snapshot taken AT spawn time is safe for an already-started child; the danger is the window between "decide to act for user A" and "read the global to act".

## Related

- [[Whole-aggregate read-modify-write for a per-child toggle causes lost updates under concurrent sibling writes]]
