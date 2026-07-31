---
title: "Verifying an API credential: 401 means invalid, 403 means valid but scope-limited"
created: 2026-07-02
type: lesson
status: seedling
source: "session 2026-07-02"
tags: [http, auth, bitbucket, atlassian, scopes, gotcha, vinnstack]
---

# Verifying an API credential: 401 means invalid, 403 means valid but scope-limited

When checking whether an API credential is valid, distinguish 401 from 403: 401 (Unauthorized) = the credential itself is bad/unknown; 403 (Forbidden) = you DID authenticate, but the specific endpoint needs a scope/permission this credential lacks. So a verification probe that returns 403 still proves the credential is valid — don't treat non-2xx as invalid wholesale. Rule: status===401 -> invalid; 2xx -> valid (+ read profile); anything else (incl. 403) -> valid but scope-limited on that endpoint.

Concrete Bitbucket case: newer Atlassian API tokens (prefix ATATT..., used with your EMAIL as the Basic-auth username, not the Bitbucket username) carry granular scopes. A token scoped for repos (read:repository, write:repository, write:pullrequest, admin:*) returns 403 on GET /2.0/user because that endpoint uniquely requires read:user:bitbucket — a scope repo automation doesn't need. The 403 body helpfully lists required vs granted scopes. Verifying such a token against /2.0/user wrongly reports it invalid; verify by 401-vs-else instead, or probe an endpoint the expected scopes cover.

Also: several unfiltered Bitbucket list endpoints (/2.0/workspaces, /2.0/repositories, /2.0/user/permissions/repositories) now return 410 Gone (CHANGE-2770 deprecation) — not auth signals. Real case: Vinnstack verifyBasic in lib/authProviders.ts.
