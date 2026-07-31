---
title: "Re-check live dependencies right before committing in a shared repo"
created: 2026-07-07
type: lesson
status: seedling
source: "vinnstack cloudbuild.yaml setup, 2026-07-07"
tags: [git, collaboration, ci, process]
---

# Re-check live dependencies right before committing in a shared repo

In a shared git working directory, a teammate can land new commits on the same branch WHILE you'\''re mid-session editing files — the branch'\''s history (and files you already read) can drift out from under you between reads, with no explicit `git pull` on your part triggering it.

Concretely: early in a session I read `scripts/build-exe.ps1` and it required a repo-root `cloud-sql-proxy.exe` (hard failure if missing), and designed a CI step around that. Partway through the same session, a teammate'\''s commit (`fbace24`, "Update Cloud SQL Auth Proxy handling: remove bundling requirement and clarify installation via gcloud components") changed that script plus `electron/main.js` to resolve the proxy purely from PATH instead — and that commit showed up in local `git log` without me doing anything. Had I not re-checked `git log`/re-read the dependency right before committing my own change, I'\''d have shipped a CI pipeline built on a stale assumption.

Lesson: before finalizing any change that depends on how another part of a live, shared codebase currently behaves — especially a CI/build pipeline — re-verify that dependency'\''s current on-disk state right before committing, not just at the start of the task.

## Related
[[Dead bundling config outlives the runtime code that read it]]
