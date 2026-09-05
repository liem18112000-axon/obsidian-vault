---
title: "gcloud --substitutions needs ^DELIM^ escape when a value contains commas"
created: 2026-08-18
type: lesson
status: seedling
source: "code review LUZ-158230 k6 load-test, 2026-08-18"
tags: [gcloud, cloud-build, gotcha, cli]
---

# gcloud --substitutions needs ^DELIM^ escape when a value contains commas

The gcloud CLI parses `--substitutions=k=v,k2=v2` as an ArgDict, splitting on `,` and `=`. So a substitution **value that itself contains commas** (e.g. a comma-separated `_TENANT_IDS=a,b,c`) gets mis-split: after the first tenant, the next token has no `=` and gcloud aborts with `Bad syntax for dict arg: [<token>]` — the build never starts.

Fix: switch the dict to a custom delimiter with the leading `^DELIM^` sentinel, then use that delimiter (not `,`) between entries:

```
gcloud builds triggers run NAME \
  --substitutions=^;^_TENANT_IDS=a,b,c;_K6_RPS=10
```

The `^;^` prefix tells gcloud "entries are separated by `;`", freeing `,` to appear literally inside a value. Applies to any gcloud flag using the ArgDict/ArgList format (`--substitutions`, `--update-labels`, etc.).

Seen in the LUZ-158230 k6 load-test harness (`k6/run_trigger.cmd`), where a multi-tenant example silently never launched a Cloud Build.

## Related

- [[Cloud Build substitutions]]
