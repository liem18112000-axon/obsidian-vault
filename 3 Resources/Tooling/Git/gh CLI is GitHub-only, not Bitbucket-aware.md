---
ai_hash: 69396b793e23a8a9
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-12
entities: []
source: Vinnstack BDD implement-stage debugging, 2026-07-12
status: seedling
tags:
- gh-cli
- bitbucket
- github
- gotcha
- vinnstack
title: gh CLI is GitHub-only, not Bitbucket-aware
type: lesson
---

# gh CLI is GitHub-only, not Bitbucket-aware

The `gh` CLI (GitHub CLI) only ever talks to GitHub-hosted remotes — pointed at a repo whose `origin` is Bitbucket (or GitLab, etc.), every `gh pr` subcommand fails outright with something like "none of the git remotes configured for this repository point to a known GitHub host," regardless of auth state or flags. It is not a generic "git forge" CLI; it has zero Bitbucket support.

Practical implication: never default to `gh` for PR automation without first confirming the remote host (check `git remote get-url origin` for `github.com` vs `bitbucket.org` vs `gitlab.com`, etc.). For a Bitbucket-hosted repo, use Bitbucket Cloud's own REST API instead (`https://api.bitbucket.org/2.0/repositories/{workspace}/{repo_slug}/pullrequests`, Basic auth with an app password) — see [[Bitbucket Cloud pull-request REST API shape]].

Hit this directly: built a "Test implementation" pipeline in Vinnstack (lib/bdd/implementGit.ts) that shelled out to `gh pr create`/`gh pr view` for opening PRs after a headless Claude run authored step-definition code. The repo (luz_docs_integration_test) is hosted on Bitbucket, not GitHub, so every PR-open attempt failed with that exact "known GitHub host" error. Fixed by replacing the `gh` shell-outs with direct `fetch` calls to Bitbucket's REST API, reusing the same Basic-auth-from-stored-credentials pattern the rest of that codebase already used for other Bitbucket calls (graphifyRunner.ts's bbAuthHeader).

%% ai-graph-start %%

**Related notes:**
- [[Bitbucket Cloud pull-request REST API shape]]
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]
- [[luz_docs_integration_test AI pipeline branch and PR mechanics]]
- [[Bitbucket PR merge lags git fetch; don't conclude not-merged from one originmain check]]
- [[Gate Terraformdeploy CI to push-on-main, not pull_request (secrets fail PRs)]]

%% ai-graph-end %%