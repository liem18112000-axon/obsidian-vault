---
title: "FETCH_HEAD is volatile when an IDE auto-fetches"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04 luz_docs LUZ-155107"
tags: [git, gotcha]
---

# FETCH_HEAD is volatile when an IDE auto-fetches

Never rely on FETCH_HEAD surviving across tool calls or terminal commands: any concurrent fetch — an IDE auto-fetch, a background poller — silently rewrites it. Mid-session a merge of FETCH_HEAD reported "Already up to date" because the IDE had re-fetched the current branch between my fetch and my merge.

**Rule:** fetch the named branch (`git fetch origin <branch>`) and merge the tracking ref (`git merge origin/<branch>`), which is stable, instead of `git merge FETCH_HEAD`.

Verify when suspicious: `git rev-parse HEAD FETCH_HEAD` — if they match unexpectedly, FETCH_HEAD was clobbered.
