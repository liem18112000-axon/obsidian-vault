---
title: "Cloud Build treats $VAR in step args as its own substitution; escape shell $ as $$"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-14"
tags: [cloud-build, ci, yaml, shell, gotcha]
---

# Cloud Build treats $VAR in step args as its own substitution; escape shell $ as $$

Gotcha (Cloud Build, 2026-07-14): a `cloudbuild.yaml` step used a shell command with `$(...)` and `${NEXT_VER}`. The build failed at CONFIG PARSE (blank SHORT_SHA, status "generic::invalid_argument: key in the template \"NEXT_VER\" is not a valid built-in substitution") because Cloud Build interprets `$VAR` / `${VAR}` in step args as ITS OWN substitutions, not shell variables.

Fix: escape shell `$` as `$$` in cloudbuild.yaml. So `$(cmd)` -> `$$(cmd)` and `${VAR}` -> `$${VAR}`. Cloud Build converts `$$` to a literal `$` before handing the string to the shell.

Bonus: a config-parse failure like this fails instantly (no build minutes burned) and has an empty SHORT_SHA in `gcloud builds list` — a quick tell that the yaml/substitution is malformed vs a real step failure.
