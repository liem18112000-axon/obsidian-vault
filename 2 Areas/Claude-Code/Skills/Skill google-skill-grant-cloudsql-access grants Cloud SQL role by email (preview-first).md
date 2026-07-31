---
title: "Skill google-skill-grant-cloudsql-access grants Cloud SQL role by email (preview-first)"
created: 2026-07-26
type: howto
status: seedling
source: "session 2026-07-26"
tags: [claude-code, skill, gcp, iam, cloudsql]
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
