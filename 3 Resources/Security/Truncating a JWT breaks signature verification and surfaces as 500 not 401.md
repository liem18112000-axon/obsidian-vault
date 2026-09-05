---
title: "Truncating a JWT breaks signature verification and surfaces as 500 not 401"
created: 2026-08-07
type: lesson
status: seedling
source: "luz_docs_import RCA 2026-08-07"
tags: [jwt, auth, debugging, gotcha, luz-docs-import]
---

# Truncating a JWT breaks signature verification and surfaces as 500 not 401

When manually copying a bearer JWT into a curl/test, altering ANY byte of the base64 **payload** (e.g. shortening a claims array like `security_classes`) invalidates the signature — the signature is computed over header.payload, so a changed payload no longer matches it.

The server then FAILS VERIFICATION, and depending on the stack that can surface as a **500, not a 401**. In luz_docs_import the chain is:
`PermissionAllowedRequestFilter.injectToken` → CDI @Produces `TokenProducer.getTokenInfo/decodeToken` → `JWTReader.withRSAPublicKeyAndEncodedToken/withEncodedToken` → `com.axonivy.sec.token.TokenException: parsing and verifying` → wrapped as `javax.enterprise.inject.CreationException` → UnexpectedExceptionMapper → **HTTP 500**.

Tell-tale that it's a transcription bug, not a real server bug: **your** curl 500s but the user's *identical* curl returns 200. That means you corrupted the token when copying it (I truncated the payload and mis-diagnosed a 'signature verification failure' / 'auth issue' that did not exist).

Rules:
- Never hand-edit or truncate a JWT. Copy it whole; verify with `echo -n "$TOKEN" | wc -c` against the known length (this token was 1703 chars).
- Prefer reading the token from a file (`-H "Authorization: Bearer $(cat tok)"`) over pasting.
- `withRSAPublicKeyAndEncodedToken` succeeding then `withEncodedToken` throwing = key was fetched fine but the token didn't verify → signature/payload mismatch (or expiry), NOT a public-key/network problem.

Related: [[luz_docs_import]].

## Related

- [[luz_docs_import]]
