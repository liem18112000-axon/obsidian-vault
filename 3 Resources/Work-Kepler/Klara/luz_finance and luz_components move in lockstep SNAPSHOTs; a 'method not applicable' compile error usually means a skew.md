---
ai_hash: db1269cdf3260d07
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-07-27
entities: []
source: session 2026-07-27 luz_finance setup
status: seedling
tags:
- klara-luz
- maven
- snapshot
- version-skew
- gotcha
- axon-ivy
title: luz_finance and luz_components move in lockstep SNAPSHOTs; a 'method not applicable'
  compile error usually means a skew
type: lesson
---

# luz_finance and luz_components move in lockstep SNAPSHOTs; a 'method not applicable' compile error usually means a skew

`luz_finance` and `luz_components` share the **same SNAPSHOT version line** (e.g. both `1.00.48.00-SNAPSHOT`) and are built together in CI. `luz_finance` compiles against classes that live *inside* `luz_components` (e.g. `RevolutDraftPaymentBean`).

**Gotcha:** a local `mvn` compile error like *'The method send(...) in type RevolutDraftPaymentBean is not applicable for the arguments (...)'* at a call site in luz_finance is almost never a luz_finance bug — it means the **resolved luz_components SNAPSHOT in your `.m2` is out of step** with what luz_finance's source expects (a method signature changed on one side but not the other). Running `mvn -U` can pull a *newer* luz_components snapshot than the luz_finance revision was written against, surfacing exactly this.

**Diagnose:** confirm the class is not in `luz_finance/src` (grep) → it's from the dependency; then check `~/.m2/repository/ch/xpertline/luz/luz_components/<ver>/` timestamp vs the luz_finance commit. **Fix belongs to the team**, not the local setup: either update the luz_finance call site, or align/rebuild the matching luz_components snapshot. Do NOT edit master call sites blindly to make it compile. See [[3 Resources/Work-Kepler/Axon Ivy/Axon Ivy project anatomy logic split across processes, data classes, HTML dialogs, and Java]].

## Related

- [[Axon Ivy project anatomy: logic split across processes]]
- [[data classes]]
- [[HTML dialogs]]
- [[and Java]]

%% ai-graph-start %%

**Related notes:**
- [[KlaraLuz Axon Ivy projects on master still target Ivy 10.0.15, not 12]]
- [[A refactor that removes a method must grep tests for its name before merging]]
- [[Axon Ivy project anatomy logic split across processes, data classes, HTML dialogs, and Java]]
- [[Run mvn test-compile after changing a recordctor signature — Cloud Build compiles tests, local mvn compile does not]]
- [[luz_epost_business_web to luz_docs_view_controller integration goes through one REST client package]]

%% ai-graph-end %%