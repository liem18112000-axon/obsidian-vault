---
ai_hash: 5961b5b8ee2bab0e
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-26
entities:
- google-skill-grant-cloudsql-access
- Cloud SQL
- Claude Code skill
- IAM role
- GCP project
- bash script
- SKILL.md
- ensure-bash.ps1
- EMAIL
- PROJECT
- klara-nonprod
- ROLE
- roles/cloudsql.client
- MEMBER_TYPE
- user
- APPLY
- add-iam-policy-binding
- grant_cloudsql_access.sh
- role cheat-sheet
- 3 Resources/Cloud/GCP/GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get
source: session 2026-07-26
status: seedling
tags:
- claude-code
- skill
- gcp
- iam
- cloudsql
title: Skill google-skill-grant-cloudsql-access grants Cloud SQL role by email (preview-first)
type: howto
---

# Skill google-skill-grant-cloudsql-access grants Cloud SQL role by email (preview-first)

Custom Claude Code skill **`google-skill-grant-cloudsql-access`** (at `~/.claude/skills/google-skill-grant-cloudsql-access/`) grants a Cloud SQL IAM role to a member on a GCP project. Built in the house `google-skill-*` style: bash script + `SKILL.md` + shared `ensure-bash.ps1` bootstrap.

- **Input:** `EMAIL` (only required arg, 1st positional).
- **Defaults:** `PROJECT=klara-nonprod`, `ROLE=roles/cloudsql.client`, `MEMBER_TYPE=user` — all overridable via env.
- **Preview-first:** a plain run prints the members current roles + the exact planned `add-iam-policy-binding` and mutates nothing; re-run with `APPLY=1` to actually grant, then it re-reads the policy to verify.
- **Idempotent:** exits early if the member already holds the role.

Run: `bash ~/.claude/skills/google-skill-grant-cloudsql-access/grant_cloudsql_access.sh EMAIL [APPLY=1]`.

See the role cheat-sheet for which `ROLE` to pass for a given intent.

## Related

- [[3 Resources/Cloud/GCP/GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get]]

%% ai-graph-start %%

**Related notes:**
- [[GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get]]
- [[Cloud SQL Auth Proxy needs roles-cloudsql.client on the connecting identity or it 403s NOT_AUTHORIZED]]

**Relations:**
- google-skill-grant-cloudsql-access — *is a* — Claude Code skill
- google-skill-grant-cloudsql-access — *grants* — Cloud SQL IAM role
- google-skill-grant-cloudsql-access — *operates on* — GCP project
- google-skill-grant-cloudsql-access — *is located at* — ~/.claude/skills/google-skill-grant-cloudsql-access/
- google-skill-grant-cloudsql-access — *is built with* — bash script
- google-skill-grant-cloudsql-access — *is built with* — SKILL.md
- google-skill-grant-cloudsql-access — *uses* — ensure-bash.ps1
- EMAIL — *is an input for* — google-skill-grant-cloudsql-access
- PROJECT — *is a default for* — google-skill-grant-cloudsql-access
- klara-nonprod — *is default value for* — PROJECT
- ROLE — *is a default for* — google-skill-grant-cloudsql-access
- roles/cloudsql.client — *is default value for* — ROLE
- MEMBER_TYPE — *is a default for* — google-skill-grant-cloudsql-access
- user — *is default value for* — MEMBER_TYPE
- google-skill-grant-cloudsql-access — *is triggered by* — APPLY=1
- google-skill-grant-cloudsql-access — *uses command* — add-iam-policy-binding
- google-skill-grant-cloudsql-access — *is run via* — grant_cloudsql_access.sh
- role cheat-sheet — *provides information for* — ROLE
- 3 Resources/Cloud/GCP/GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get — *is a related resource to* — google-skill-grant-cloudsql-access
- 3 Resources/Cloud/GCP/GCP Cloud SQL IAM role cheat-sheet which role grants cloudsql.instances.get — *is a* — role cheat-sheet
- google-skill-grant-cloudsql-access — *has property* — Preview-first
- google-skill-grant-cloudsql-access — *has property* — Idempotent

%% ai-graph-end %%