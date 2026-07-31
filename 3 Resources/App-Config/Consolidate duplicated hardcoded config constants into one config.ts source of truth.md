---
title: "Consolidate duplicated hardcoded config constants into one config.ts source of truth"
created: 2026-07-09
type: lesson
status: seedling
source: "vinnstack session 2026-07-09"
tags: [nextjs, config, refactoring, env-vars]
---

# Consolidate duplicated hardcoded config constants into one config.ts source of truth

When the same deployment endpoint (a hostname, workspace slug, repo/branch name) is hardcoded as a duplicated `const` across several files, the fix is to add ONE field to the app's central config module (e.g. `getConfig()`/`StoredConfig` in `lib/core/config.ts`) with the same `stored file > env var > built-in default` precedence every other config field already uses, then delete every duplicate `const` and read the value from `getConfig()` at each call site instead.

Concretely: `const JIRA_HOST = "https://axonivy.atlassian.net"` existed as FOUR independent copies in a Next.js app (two server-side `.ts` files, two client-side `.tsx` components) — one of which already had a DIFFERENT env-var fallback (`ATLASSIAN_JIRA_HOST`) than the others (no fallback at all), so the copies had already drifted in behavior, not just duplication risk. Consolidating them: add `jiraHost?: string` to `StoredConfig`, resolve it once in `getConfig()` as `clean(stored) ?? clean(process.env.ATLASSIAN_JIRA_HOST) ?? "https://axonivy.atlassian.net"`, then replace each of the four `const` declarations with `getConfig().jiraHost` (server-side) or a small client hook that fetches the resolved value from a `/api/settings`-style endpoint once on mount (client-side, since env vars aren't readable in the browser).

For a PURE/unit-tested function that used the hardcoded constant internally (e.g. `composeFlowExportMarkdown` in `lib/interrogation/flowExport.ts`), the right move is to add the resolved value as an explicit parameter on its input object rather than importing the config module into the pure function — keeps it testable without mocking config, and the impure caller (`exportStoryFlow`) supplies `getConfig().jiraHost` when it calls in.

One field is legitimately NOT a candidate for this treatment: a hardcoded allowlist of exact values that was deliberately security-reviewed (e.g. a fixed list of Bitbucket repo slugs gating credential use, documented in a security doc as a reviewed boundary) should stay hardcoded — turning a reviewed allowlist into a runtime-editable setting weakens the control it exists to enforce.

Related: exposing these same fields in a Settings UI (a new tab, pre-filled with the CURRENT resolved value rather than blank-with-placeholder) is a natural pairing — see [[Prefill a settings field with the resolved effective value, not blank-with-placeholder]] if that note exists, otherwise this is the companion half of that idea.
