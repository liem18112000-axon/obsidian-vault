---
title: "Log red herrings: enclosing class name and baseline-noise lines"
created: 2026-06-30
type: lesson
status: seedling
source: "PROD jwt-service investigation 2026-06-30"
tags: [diagnostics, root-cause, logs, gotcha]
---

# Log red herrings: enclosing class name and baseline-noise lines

Two log traps that sent the first-pass root cause of the 2026-06-30 PROD incident in the wrong direction (toward Keycloak), both worth a standing habit:

1. **The enclosing class name is not the failing call.** A timeout stack inside `KeycloakRefreshTokenResource` / a method named `generateAccessTokenByKeyCloakForMylifeEpost` looked like a Keycloak hang — but the *inner* failing call was to `luztenant-service` `/security-classes`. Read the actual failing frame / outbound URL, not the resource/method name that wraps it.

2. **Baseline-noise lines masquerade as the signal.** Keycloaks `400 invalid_grant "Offline user session not found"` looked incident-related, but counting it per 30-min window showed it hit the row cap in EVERY window — the day before, pre-incident, during, and after. A constant signal cannot explain a transient episode. Always rate-compare a suspect log line against pre/post-incident windows before treating it as evidence.

General rule: confirm a dependency was actually slow by measuring ITS latency in-window vs a calm window (e.g. Keycloak round-trips were 72–215ms throughout — not the blocker), rather than inferring from the presence of an error string. Related: [[Cascading DC: follow the timeout chain one layer down]], [[Off-mesh services (istio inject=false) have no Istio access logs]].

## Related

- [[Cascading DC: follow the timeout chain one layer down]]
- [[Off-mesh services (istio inject=false) have no Istio access logs]]
