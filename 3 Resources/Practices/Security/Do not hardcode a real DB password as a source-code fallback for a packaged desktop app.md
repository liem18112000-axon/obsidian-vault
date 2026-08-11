---
ai_hash: ca0024e540feadee
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-07
entities: []
source: Vinnstack session 2026-07-07
status: seedling
tags:
- security
- secrets
- vinnstack
- electron
- decision-making
title: Do not hardcode a real DB password as a source-code fallback for a packaged
  desktop app
type: lesson
---

# Do not hardcode a real DB password as a source-code fallback for a packaged desktop app

Never hardcode a live credential as a "works with no .env" default in an app that gets packaged and distributed (electron-builder installer, exe, container image) — and if asked to, name the exposure mechanism before complying or refusing.

**Two exposures a `.env` does not have:**
1. **Git history is permanent** — deleting the line later does not undo it; only rotating the credential does.
2. **It ships in every build** — anyone who unpacks the installer or runs `strings` on the bundled server code has the live secret. A `.env` exists only in developer working copies.

**Instance:** Vinnstack was asked to hardcode the real Postgres `postgres` superuser password for the shared klara-nonprod Cloud SQL instance into `lib/config.ts`. Spelling out the specifics — superuser, shared instance, git history, every future packaged Electron build — plus the middle option (a scoped low-privilege role instead of superuser) led the user to pick no hardcoded fallback at all (require env / `config.json`).

**Transferable rule:** for hard-to-reverse security requests, state the SPECIFIC mechanism (superuser vs scoped role, git history vs local file, one dev machine vs every shipped copy) and offer real options. Concrete specifics change the decision; "this is insecure" does not.

%% ai-graph-start %%

**Related notes:**
- [[Open-and-degrade beats hard-quit let a desktop app start without its optional DB (Vinnstack clean-Win fix)]]
- [[Vinnstack bundles cloud-sql-proxy.exe as a gitignored extraResource]]
- [[Testing the packaged Vinnstack exe needs databaseUrl in config.json, pins port 3001, portable stub doesn't inherit ad-hoc env]]
- [[Dead bundling config outlives the runtime code that read it]]
- [[Vinnstack desktop app dropped Google OAuth for a typed-email operator identity]]

%% ai-graph-end %%