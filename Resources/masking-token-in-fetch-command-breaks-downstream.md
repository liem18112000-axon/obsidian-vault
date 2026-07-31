---
title: Don't sed-mask a token in the same command that saves it — downstream reads the mask
tags: [gotcha, shell, tokens, benchmarking, luz]
created: 2026-07-17
type: resource
---

# Masking a secret at capture time corrupts the saved copy

When fetching a bearer token to reuse later, I piped the fetch through `sed` to mask the JWT for display **and** that was the only capture of it. The task output file then contained the *masked* string (`eyJ0eXAiO…MASKED…`, ~17 chars), not the real 1799-char JWT. A downstream benchmark script that read the token from that file sent the masked garbage → every API call returned **HTTP 401** (looked like an auth/permission problem, wasn't).

## Rule
Separate **capture** from **display**:
- Save the RAW value to a file, then mask only when echoing to the human.
  ```bash
  get_token.sh "$ADMIN_TENANT" > tok.full 2>&1
  grep -aE '^eyJ' tok.full | head -1 > token.txt   # raw, unmasked, on disk
  # sanity WITHOUT printing the value:
  echo "len=$(tr -d '\n' < token.txt | wc -c) segs=$(tr -d '\n' < token.txt | awk -F. '{print NF}')"
  ```
- A valid JWT is long (~1000-2000 chars) and has exactly **3 dot-separated segments**. If a "token" is ~17 chars or has ≠3 segments, it's not a JWT — check your capture pipeline before blaming auth.

## Debug signature
Undertow's plain `<html>…Unauthorized…</html>` 401 = rejected at the security filter (bad/short token), before JAX-RS. A later, valid token flipped the same endpoint to 503 (a real app/downstream condition) — proving auth was the earlier issue.

Related: `luz-skill-get-token` needs an admin tenant id as 1st arg (e.g. `00a04daf-f2b3-41d5-8c12-2d1b4c48a36a`), type=all-tenant.
