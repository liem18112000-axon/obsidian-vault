---
title: "Killing a backgrounded Bash tool call doesn't guarantee its child processes die"
created: 2026-07-23
type: lesson
source: "session 2026-07-23, perf tenant seeding to 2M docs"
tags: [bash, windows, process, gotcha, mongodb]
---

# Killing a backgrounded Bash tool call doesn't guarantee its child processes die

When a long-running backgrounded shell command gets stopped/killed (hit a timeout, or an explicit stop), that only guarantees the TOP-LEVEL shell process the tool tracks is gone -- it does NOT guarantee every child process it spawned is also gone. On Windows/Git Bash specifically, a bash script that launches a Node.js child process (even via plain `node script.js`, no explicit backgrounding with `&`) can leave that Node process running, fully alive and still doing its work, completely detached from the parent shell -- because process-group/job-control semantics that would normally let a SIGTERM cascade to children don't reliably apply across the Windows/MSYS boundary.

Consequence, concretely: ran a Mongo-seeding script 5 times in a row (each earlier one reported as 'killed' by the tool after its timeout), assuming each prior attempt had actually stopped before starting the next. It hadn't -- `ps aux` revealed FIVE separate live `node` processes, one per attempt, all still running and all still inserting documents into the same collection concurrently. Total inserted overshot the intended target by ~200k documents before this was caught (via `ps aux | grep node` and a live document-count check that was far higher than any single attempt's own logged progress could explain).

Rule to follow now: after ANY backgrounded command reports 'killed'/'stopped' when it spawned a subprocess (especially a long-running seeder, migration, or load-generator), verify with `ps aux` (or the platform equivalent) that the actual worker process is gone before assuming it's safe to launch a replacement/resumption of the same job. If it's still there, `kill -9` it explicitly rather than trusting the tool's own status label. This matters most for scripts that mutate shared/expensive state (a live database, a cloud resource) where 'a bit of double-work' is not harmless.
