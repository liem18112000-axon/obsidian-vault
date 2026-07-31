---
ai_hash: 97f007e2a33368b2
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-30
entities: []
source: PROD jwt-service investigation 2026-06-30
status: seedling
tags:
- diagnostics
- root-cause
- logs
- gotcha
title: 'Log red herrings: enclosing class name and baseline-noise lines'
type: lesson
---

# Log red herrings: enclosing class name and baseline-noise lines

Two log traps that sent the first-pass root cause of the 2026-06-30 PROD incident in the wrong direction (toward Keycloak), both worth a standing habit:

1. **The enclosing class name is not the failing call.** A timeout stack inside `KeycloakRefreshTokenResource` / a method named `generateAccessTokenByKeyCloakForMylifeEpost` looked like a Keycloak hang — but the *inner* failing call was to `luztenant-service` `/security-classes`. Read the actual failing frame / outbound URL, not the resource/method name that wraps it.

2. **Baseline-noise lines masquerade as the signal.** Keycloaks `400 invalid_grant "Offline user session not found"` looked incident-related, but counting it per 30-min window showed it hit the row cap in EVERY window — the day before, pre-incident, during, and after. A constant signal cannot explain a transient episode. Always rate-compare a suspect log line against pre/post-incident windows before treating it as evidence.

General rule: confirm a dependency was actually slow by measuring ITS latency in-window vs a calm window (e.g. Keycloak round-trips were 72–215ms throughout — not the blocker), rather than inferring from the presence of an error string. Related: [[3 Resources/Infra/Observability/Cascading DC follow the timeout chain one layer down]], [[Off-mesh services (istio inject=false) have no Istio access logs]].

## Related

- [[3 Resources/Infra/Observability/Cascading DC follow the timeout chain one layer down]]
- [[Off-mesh services (istio inject=false) have no Istio access logs]]

%% ai-graph-start %%

**Related notes:**
- [[Cascading DC follow the timeout chain one layer down]]
- [[Istio DC response_flag with round latency = caller read timeout]]
- [[Off-mesh services (istio inject=false) have no Istio access logs]]
- [[Downstream timeout must sit well below caller timeout (fail-fast ladder)]]
- [[jwt-service token path synchronously calls luztenant security-classes]]

%% ai-graph-end %%