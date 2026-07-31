---
ai_hash: ddc26204a7b9da39
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-15
entities: []
source: session 2026-07-14
status: seedling
tags:
- cloud-build
- ci
- yaml
- shell
- gotcha
title: Cloud Build treats $VAR in step args as its own substitution; escape shell
  $ as $$
type: lesson
---

# Cloud Build treats $VAR in step args as its own substitution; escape shell $ as $$

Gotcha (Cloud Build, 2026-07-14): a `cloudbuild.yaml` step used a shell command with `$(...)` and `${NEXT_VER}`. The build failed at CONFIG PARSE (blank SHORT_SHA, status "generic::invalid_argument: key in the template \"NEXT_VER\" is not a valid built-in substitution") because Cloud Build interprets `$VAR` / `${VAR}` in step args as ITS OWN substitutions, not shell variables.

Fix: escape shell `$` as `$$` in cloudbuild.yaml. So `$(cmd)` -> `$$(cmd)` and `${VAR}` -> `$${VAR}`. Cloud Build converts `$$` to a literal `$` before handing the string to the shell.

Bonus: a config-parse failure like this fails instantly (no build minutes burned) and has an empty SHORT_SHA in `gcloud builds list` — a quick tell that the yaml/substitution is malformed vs a real step failure.

%% ai-graph-start %%

**Related notes:**
- [[Cloud Build $COMMIT_SHA is the full 40-char git SHA, not the short one]]
- [[Terraform templatefile parses dollar-brace even inside comments]]
- [[Diagnose Cloud Build failures with gcloud builds describe and log]]
- [[Google Cloud Build steps share one workspace volume across images]]
- [[PowerShell 5.1 eats inner double-quotes passed to native exes like gcloud]]

%% ai-graph-end %%