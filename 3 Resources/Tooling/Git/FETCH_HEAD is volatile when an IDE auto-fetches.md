---
ai_hash: 97113db585935358
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities: []
source: session 2026-06-04 luz_docs LUZ-155107
status: seedling
tags:
- git
- gotcha
title: FETCH_HEAD is volatile when an IDE auto-fetches
type: lesson
---

# FETCH_HEAD is volatile when an IDE auto-fetches

Never rely on FETCH_HEAD surviving across tool calls or terminal commands: any concurrent fetch — an IDE auto-fetch, a background poller — silently rewrites it. Mid-session a merge of FETCH_HEAD reported "Already up to date" because the IDE had re-fetched the current branch between my fetch and my merge.

**Rule:** fetch the named branch (`git fetch origin <branch>`) and merge the tracking ref (`git merge origin/<branch>`), which is stable, instead of `git merge FETCH_HEAD`.

Verify when suspicious: `git rev-parse HEAD FETCH_HEAD` — if they match unexpectedly, FETCH_HEAD was clobbered.

%% ai-graph-start %%

**Related notes:**
- [[A concurrent session's git stash can silently revert your in-progress edits]]
- [[git submodule update --remote can clobber unpushed submodule HEAD]]
- [[Bitbucket PR merge lags git fetch; don't conclude not-merged from one originmain check]]
- [[Branch created from current HEAD drags unrelated commits — verify against originmaster]]
- [[Re-check live dependencies right before committing in a shared repo]]

%% ai-graph-end %%