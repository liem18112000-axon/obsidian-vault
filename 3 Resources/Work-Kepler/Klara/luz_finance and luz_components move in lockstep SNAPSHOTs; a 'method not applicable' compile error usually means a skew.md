---
title: "luz_finance and luz_components move in lockstep SNAPSHOTs; a 'method not applicable' compile error usually means a skew"
created: 2026-07-27
type: lesson
status: seedling
source: "session 2026-07-27 luz_finance setup"
tags: [klara-luz, maven, snapshot, version-skew, gotcha, axon-ivy]
---

# luz_finance and luz_components move in lockstep SNAPSHOTs; a 'method not applicable' compile error usually means a skew

`luz_finance` and `luz_components` share the **same SNAPSHOT version line** (e.g. both `1.00.48.00-SNAPSHOT`) and are built together in CI. `luz_finance` compiles against classes that live *inside* `luz_components` (e.g. `RevolutDraftPaymentBean`).

**Gotcha:** a local `mvn` compile error like *'The method send(...) in type RevolutDraftPaymentBean is not applicable for the arguments (...)'* at a call site in luz_finance is almost never a luz_finance bug — it means the **resolved luz_components SNAPSHOT in your `.m2` is out of step** with what luz_finance's source expects (a method signature changed on one side but not the other). Running `mvn -U` can pull a *newer* luz_components snapshot than the luz_finance revision was written against, surfacing exactly this.

**Diagnose:** confirm the class is not in `luz_finance/src` (grep) → it's from the dependency; then check `~/.m2/repository/ch/xpertline/luz/luz_components/<ver>/` timestamp vs the luz_finance commit. **Fix belongs to the team**, not the local setup: either update the luz_finance call site, or align/rebuild the matching luz_components snapshot. Do NOT edit master call sites blindly to make it compile. See [[Axon Ivy project anatomy: logic split across processes, data classes, HTML dialogs, and Java]].

## Related

- [[Axon Ivy project anatomy: logic split across processes]]
- [[data classes]]
- [[HTML dialogs]]
- [[and Java]]
