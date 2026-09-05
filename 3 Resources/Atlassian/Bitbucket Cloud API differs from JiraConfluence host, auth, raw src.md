---
title: "Bitbucket Cloud API differs from Jira/Confluence: host, auth, raw src"
created: 2026-08-27
type: lesson
status: seedling
source: "session 2026-08-27 — kga atlassian package"
tags: [bitbucket, atlassian, api, mixin, python]
---

# Bitbucket Cloud API differs from Jira/Confluence: host, auth, raw src

Bitbucket is Atlassian but its Cloud REST API is a **different realm** from Jira/Confluence — three gotchas:
- **Host:** `https://api.bitbucket.org/2.0` (not the `*.atlassian.net` site base).
- **Auth:** Basic auth with **username + app password** (or a repo/workspace access token as Bearer) — the Jira email + API-token pair does NOT authenticate Bitbucket.
- **Raw file content:** `GET /2.0/repositories/{ws}/{repo}/src/{ref}/{path}` returns the **raw file text**, not JSON — read `resp.text`, and do not force `Accept: application/json`.

**Design pattern for a multi-service read-only client:** put the config + retry core (`_request(url, auth)`, `_get(path)`) on one `BaseClient`, and add each service as a thin **mixin** (`JiraMixin`, `ConfluenceMixin`, `BitbucketMixin`) whose methods call `self._request/_get`. Compose `class AtlassianClient(JiraMixin, ConfluenceMixin, BitbucketMixin, BaseClient)`. One retry core, no duplication, and the public API stays a single client. Package it as `atlassian/` (base + one module per service) so each service is isolated. Gotcha when splitting: a test that `monkeypatch.setattr("kga.atlassian.asyncio.sleep", ...)` must move to `kga.atlassian.base.asyncio.sleep` after `asyncio` relocates into `base.py`.

Context: kga `atlassian/` package (LUZ-159671 test-agent).

## Related

- [[Extracting every link from Jira ADF and Confluence storage]]
