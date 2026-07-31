---
title: "Consolidate duplicated hardcoded config constants into one config.ts source of truth"
created: 2026-07-09
updated: 2026-07-31
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [nextjs, config, refactoring, env-vars]
---

# Consolidate duplicated hardcoded config constants into one config.ts source of truth

When a deployment endpoint (hostname, workspace slug, repo/branch) is hardcoded as a duplicated `const` across files, add ONE field to the central config module (`StoredConfig` + `getConfig()` in `lib/core/config.ts`) using the app's existing precedence — **stored file > env var > built-in default** — then delete every duplicate and read from `getConfig()`.

The duplication is not just a risk, it drifts: `const JIRA_HOST = "https://axonivy.atlassian.net"` existed in FOUR copies (2 server `.ts`, 2 client `.tsx`), and only one of them honoured the `ATLASSIAN_JIRA_HOST` env var — so behaviour already differed per call site.

Shape of the fix:
- `jiraHost?: string` on `StoredConfig`; resolve once: `clean(stored) ?? clean(process.env.ATLASSIAN_JIRA_HOST) ?? "https://axonivy.atlassian.net"`.
- Server call sites → `getConfig().jiraHost`.
- Client call sites → a small hook fetching the resolved value from a `/api/settings` endpoint on mount (env vars are not readable in the browser).
- **Pure/unit-tested functions** (e.g. `composeFlowExportMarkdown`) take the resolved value as an explicit input-object parameter instead of importing the config module — keeps them testable without mocking; the impure caller passes `getConfig().jiraHost` in.

**Exception:** a deliberately security-reviewed allowlist (e.g. the fixed list of Bitbucket repo slugs gating credential use) stays hardcoded — making a reviewed boundary runtime-editable weakens the control.

## Related

- [[Prefill a settings field with the resolved effective value, not blank-with-placeholder]]
