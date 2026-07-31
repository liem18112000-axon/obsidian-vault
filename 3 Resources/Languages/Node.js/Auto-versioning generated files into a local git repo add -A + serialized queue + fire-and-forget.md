---
ai_hash: c191b70a1fe54372
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-01
entities: []
source: session 2026-07-01 (Vinnstack content repo + PRD iteration)
status: seedling
tags:
- git
- nodejs
- nextjs
- versioning
- vinnstack
- pattern
title: 'Auto-versioning generated files into a local git repo: add -A + serialized
  queue + fire-and-forget'
type: howto
---

# Auto-versioning generated files into a local git repo: add -A + serialized queue + fire-and-forget

When a Node/Next app must auto-version files it generates (docs, diagrams, JSON records) into a local git repo, a robust minimal pattern:

- **Trigger commits from writes, but stage with `git add -A`.** Any single writer that fires a commit sweeps up ALL pending changes in the tree — so you don't have to wire every writer; one hot path keeps the whole folder committed. (Vinnstack: only saveInterrogation triggers autoCommit, yet chats/skills/PRDs get committed too.)
- **Serialize git mutations.** Concurrent add/commit in one repo corrupts the index. Use a module-level promise chain (`queue = queue.then(fn); return run`) so commits run one at a time.
- **Fire-and-forget on hot paths.** autoCommit() returns void and swallows errors so a git hiccup never breaks the app write; a status endpoint surfaces state.
- **Lazy init + identity fallback.** On first use: mkdir, `git init`, and if global user.name/email are unset, set a local fallback so commits never fail on a fresh box.
- **Best-effort push.** Push only if a remote is configured; a failed push (offline/auth) is non-fatal — the local history is the source of truth.
- **Its own repo, separate from app source.** Generated content is the user's data, not product code; keep it in its own repo (here: under the vault) so it never mixes with the app tree.

Also: a PRD/artifact 'decline → refine' loop is cleanest when decline just feeds human feedback + the prior artifact back into the same generator as a 'revision request', appends a new revision (keep all versions + the feedback that produced each), and resets status to draft — reusing the generate path rather than adding a separate flow.

%% ai-graph-start %%

**Related notes:**
- [[Vinnstack withholds gitgh from the model in BDD step implementation]]
- [[vinnstack BDD pipeline stops at JiraXray, never writes files into a cloned repo]]
- [[Promise-chain queueTail pattern serializes async jobs with instant enqueue]]
- [[Regenerate-from-review-feedback pattern reuse the branchPR, don't open a new one]]
- [[Version artifacts by lifecycle event with content-dedupe, store in DB not files]]

%% ai-graph-end %%