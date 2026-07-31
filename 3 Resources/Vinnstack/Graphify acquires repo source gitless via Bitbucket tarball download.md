---
title: "Graphify acquires repo source gitless via Bitbucket tarball download"
created: 2026-07-07
type: lesson
status: seedling
source: "session 2026-07-07"
tags: [vinnstack, graphify, security, bitbucket]
---

# Graphify acquires repo source gitless via Bitbucket tarball download

Graphify (Vinnstack's code-graph feature) never runs `git clone` — it acquires each repo's source gitlessly, via a Bitbucket tarball download over HTTPS.

For each hardcoded repo slug (see `REPO_SLUGS` in `lib/graphifyRunner.ts`, 18 fixed `axonivy-prod` repos — not a dynamic/user-supplied list), the runner:
1. Looks up the default branch via the Bitbucket REST API: `GET https://api.bitbucket.org/2.0/repositories/{workspace}/{slug}`.
2. Downloads a tarball of that branch: `GET https://bitbucket.org/{workspace}/{slug}/get/{branch}.tar.gz`, authenticated with Basic auth built from `ATLASSIAN_BITBUCKET_USERNAME` / `ATLASSIAN_BITBUCKET_APP_PASSWORD`.
3. Extracts it locally with the OS's built-in `tar.exe` (no npm/git dependency).

This is a deliberate IT-Security control, not an oversight (the file header cites an internal Confluence page number as the source of the constraint): no git binary is present on the scanning host, and no writable clone is ever created. The extracted repo and its staged code-only copy are deleted again immediately after each scan finishes, whether it succeeded or failed — so no source is ever left at rest on disk. Only the derived output (`graph.json` / `slim.json`) persists.

Worth remembering because it inverts the default assumption: a tool that scans "a repo" usually shells out to `git clone` under the hood. Here that path is intentionally closed off — tarball-over-HTTPS is the *only* sanctioned acquisition method, enforced in one file (`lib/graphifyRunner.ts`) that is documented as the sole entry point for invoking graphify at all.
