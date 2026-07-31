---
title: "Local-app provider sign-in: drive the vendor CLI; Vertex is the exception (gcloud ADC + project/region)"
created: 2026-07-02
type: lesson
status: seedling
source: "session 2026-07-02"
tags: [oauth, cli, gh, gcloud, vertex, providers, vinnstack]
---

# Local-app provider sign-in: drive the vendor CLI; Vertex is the exception (gcloud ADC + project/region)

For a local desktop app, "add OAuth sign-in for provider X" is usually best implemented by driving the vendor's own CLI, not by writing an OAuth client — when the CLI exists and manages its own token store:
- Claude: claude auth {status,login,logout} (subscription OAuth; status is JSON).
- Google Cloud: gcloud auth {list,login,revoke} + application-default login for ADC.
- GitHub: gh auth {status,login --web,logout}. gh manages its token in the OS keyring; status prints (to stderr) "Logged in to github.com account <name>" + token scopes — parse that. Covers repo/Copilot access.
Each: login() spawns the CLI browser flow; status() is a quick probe; the app stores nothing (the CLI owns the token).

Vertex AI (Claude on GCP) is the exception with NO dedicated login: it authenticates via gcloud application-default credentials (the same ADC as the Google Cloud sign-in) plus a GCP project + region. So a "Vertex" connection is config-only (project + region, persisted) whose status just checks ADC presence (the application_default_credentials.json file) — there is no vertex-specific token to obtain.

Design pattern that made this cheap: a provider registry where each provider is either browserLogin (drives a CLI) or credentialFields (typed + verified + persisted). A generic Connections UI renders both, so adding a provider is one object in the registry — no UI change. Real case: Vinnstack lib/authProviders.ts (claude, google-cloud, vertex, bitbucket, atlassian, github).
