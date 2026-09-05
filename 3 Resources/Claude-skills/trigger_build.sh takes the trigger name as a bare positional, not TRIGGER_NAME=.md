---
title: "trigger_build.sh takes the trigger name as a bare positional, not TRIGGER_NAME="
created: 2026-09-03
type: gotcha
status: seedling
source: "session 2026-09-03 LUZ-157476"
tags: [claude-skills, cloud-build, gcloud, gotcha]
---

# trigger_build.sh takes the trigger name as a bare positional, not TRIGGER_NAME=

The `google-skill-trigger-cloud-build` skill script `trigger_build.sh` resolves the Cloud Build trigger name from the **first bare positional argument**, and does NOT parse a leading `TRIGGER_NAME=...` `KEY=VALUE` token (despite the SKILL.md implying KEY=VALUE pairs are accepted). Passing `trigger_build.sh TRIGGER_NAME=luz-store BRANCH=...` sets the trigger name literally to the string `TRIGGER_NAME=luz-store` and ignores `BRANCH=` — the run then targets a non-existent trigger / bogus Artifact Registry image path.

**Correct invocation:** trigger name is the bare positional; other vars go as environment-variable prefixes:

```bash
BRANCH=<branch> bash ~/.claude/skills/google-skill-trigger-cloud-build/trigger_build.sh luz-store
```

Confirmed 2026-09-03 while triggering the `luz-store` build for branch `mt-receive/LUZ-157476-fix-failure-category-test` — the KEY=VALUE form printed `name = TRIGGER_NAME=luz-store` and did nothing useful; the bare-positional + `BRANCH=` env form ran build `c0454bf8` to SUCCESS.

Related: [[FailureCategoryTest hard-codes rendered i18n strings so properties edits break it]].
