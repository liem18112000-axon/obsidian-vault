---
title: "Bitbucket cached git token 401s on REST API; PR listing needs app password"
created: 2026-08-18
type: lesson
status: seedling
source: "session 2026-08-18"
tags: [bitbucket, git, api, auth, gotcha]
---

# Bitbucket cached git token 401s on REST API; PR listing needs app password

The cached git HTTPS credential for `bitbucket.org` (axonivy-prod repos) is a **repo-scoped token**: it authorizes `git clone/fetch/push`, but returns **HTTP 401** on `api.bitbucket.org` REST endpoints such as `/2.0/repositories/axonivy-prod/<repo>/pullrequests`.

Listing **OPEN or DECLINED** pull requests via the REST API requires a Bitbucket **app password** (or OAuth token) with the `pullrequest:read` scope — the git credential is not enough.

**Consequence:** from git alone you can only recover **merged** PRs, by grepping commit subjects for `Merged in <branch> (pull request #N)`. Open/declined PRs need the Bitbucket web UI or an app-password-authed API call. Verify the credential type before assuming the API will work: `git credential fill` gives a token that clones fine yet 401s on REST.

See [[Finding intentional k8s config PRs in luz_kubernetes: filter out image-hash + merge drift]] for the merged-PR recovery technique.

## Related

- [[Finding intentional k8s config PRs in luz_kubernetes: filter out image-hash + merge drift]]
