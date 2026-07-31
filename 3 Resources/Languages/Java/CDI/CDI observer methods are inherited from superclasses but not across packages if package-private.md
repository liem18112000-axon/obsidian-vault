---
title: "CDI observer methods are inherited from superclasses but not across packages if package-private"
created: 2026-06-05
type: lesson
status: budding
source: "DocumentChangeObserver base-class refactor, session 2026-06-05"
tags: [java, cdi, events, gotcha, luz-docs]
---

# CDI observer methods are inherited from superclasses but not across packages if package-private

A CDI bean inherits `@Observes`/`@ObservesAsync` observer methods declared in its superclass (CDI member-level inheritance, spec 4.2) — unless the subclass overrides the method, in which case the override must re-declare the annotation. This makes a template-method base observer work: an abstract (non-bean) base declares the observer method calling `shouldSkip()`/`handle()` hooks, and each `@ApplicationScoped` subclass just implements the hooks.

Gotcha: Java inheritance rules still apply — a **package-private** observer method in the base is *not inherited* by a subclass in a different package, so CDI never sees it on the subclass bean. Declare the base observer method `protected` (or `public`) when subclasses live in other packages. Used for `DocumentChangeObserver` (tracking) ← `DocMaterializeObserver` (materialize) in luz_docs.

## Related

- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]
