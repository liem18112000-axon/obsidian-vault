---
title: "Clone a Bitbucket repo with an app password without leaking it (inline credential helper)"
created: 2026-07-27
type: howto
status: seedling
source: "session 2026-07-27 (Ivy setup)"
tags: [git, bitbucket, credentials, app-password, security, howto]
---

# Clone a Bitbucket repo with an app password without leaking it (inline credential helper)

To clone/pull a private Bitbucket Cloud repo over HTTPS with an app password **without exposing the secret** on a command line, in `url_effective`, or on disk, feed it through a git **inline credential helper that reads an env var**:

```bash
HELPER='!f() { echo "username=liemdoanvanthanh"; echo "password=$ATLASSIAN_BITBUCKET_APP_PASSWORD"; }; f'
git -c credential.helper= -c credential.helper="$HELPER" \
    clone "https://bitbucket.org/<workspace>/<repo>.git" "<dest>"
```

Why it is safe: the helper string is single-quoted, so the parent shell never expands `$ATLASSIAN_BITBUCKET_APP_PASSWORD`; git runs the helper via `sh` (which inherits the env) and the emitted `password=...` line goes to git, not the terminal. The leading `-c credential.helper=` clears any previously configured/stored helper so a stale credential is not tried first.

**Auth gotchas (Bitbucket Cloud app passwords):**
- The username must be the **Bitbucket account username** (e.g. `liemdoanvanthanh`), NOT the email — even if a `*_USERNAME` env var stores the email.
- App passwords are created at bitbucket.org > Personal settings > App passwords, and need the `Repositories: Read` scope to clone.

Contrast with embedding creds in the URL (`https://user:pass@host/...`), which leaks into shell history, git remotes, and progress output. See also [[Read a private Confluence page via REST API with ATLASSIAN API token]] for the matching `curl -u` token-leak gotcha.

## Related
[[Read a private Confluence page via REST API with ATLASSIAN API token]]
[[Resources index]]

## Related

- [[Read a private Confluence page via REST API with ATLASSIAN API token]]
- [[Resources index]]
