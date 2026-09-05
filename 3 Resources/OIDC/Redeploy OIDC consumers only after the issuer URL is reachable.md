---
title: "Redeploy OIDC consumers only after the issuer URL is reachable"
created: 2026-08-19
type: lesson
status: seedling
source: "leo-customer360 beta.leocdp.com cutover, 2026-08"
tags: [oidc, keycloak, oauth2-proxy, deployment, cutover]
---

# Redeploy OIDC consumers only after the issuer URL is reachable

When you change an OIDC issuer/public URL, redeploy the **relying parties** (oauth2-proxy, an API doing token introspection/JWKS) only AFTER that issuer is actually reachable over the new URL — because they fetch `<issuer>/.well-known/openid-configuration` at **startup**. oauth2-proxy in particular crash-loops if discovery fails, so bringing it up before the front door (proxy/LB/cert) is live breaks the whole cutover.

**Safe cutover order (domain/TLS switch):** (1) reconfigure the IdP hostname; (2) stand up the proxy/LB that serves it; (3) `curl <issuer>/.well-known/openid-configuration` to confirm; (4) THEN redeploy the relying parties. The IdP (e.g. Keycloak) itself can restart first — it only stamps the new hostname into generated URLs, it does not need the public URL reachable to boot.

Related: [[Keycloak behind a TLS-terminating proxy needs proxy headers and hostname-strict off]]

## Related

- [[Keycloak behind a TLS-terminating proxy needs proxy headers and hostname-strict off]]
