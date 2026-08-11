---
ai_hash: 7a119c1f034d5e1b
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-17
entities: []
tags:
- gotcha
- shell
- tokens
- benchmarking
- luz
title: Don't sed-mask a token in the same command that saves it — downstream reads
  the mask
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

%% ai-graph-start %%

**Related notes:**
- [[Prefer pasting a token minted once over scraping it from a PTY relay]]
- [[Deliver a CI-minted credential via a masked short-retention artifact, not the run log]]
- [[jwt-service token path synchronously calls luztenant security-classes]]
- [[Log red herrings enclosing class name and baseline-noise lines]]
- [[Klara app API-key to token exchange flow (jwt-service to luztenant-service)]]

%% ai-graph-end %%