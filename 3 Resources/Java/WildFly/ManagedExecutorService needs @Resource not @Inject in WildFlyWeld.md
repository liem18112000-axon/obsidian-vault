---
title: "ManagedExecutorService needs @Resource not @Inject in WildFly/Weld"
created: 2026-06-09
type: gotcha
status: seedling
source: "session 2026-06-09"
tags: [wildfly, weld, cdi, jakarta-ee, luz-docs, gotcha]
---

# ManagedExecutorService needs @Resource not @Inject in WildFly/Weld

In WildFly/Weld (Jakarta EE — luz_docs), `ManagedExecutorService` cannot be injected with CDI `@Inject`. Weld has no `@Default` producer for it, so the deployment aborts at `WeldStartService`:

```
WELD-001408: Unsatisfied dependencies for type ManagedExecutorService with qualifiers @Default
  at @Inject ch.klara.luz.docs.materialize.MaterializeMigrationExecutor.executor
```

Inject it via **`@Resource`** (JNDI resource injection) instead — the executor comes from the container, not the CDI bean manager. `MaterializeFolderParentChangeService` already does it right:

```java
@Resource
private ManagedExecutorService executor;
```

**Why it bites silently:** compiles fine; only fails at deploy time. On the pod the symptom chain is: WAR deploy fails → `luz_docs.war.failed` marker appears in `standalone/deployments/` → startup probe `cat .../luz_docs.war.deployed` keeps failing → kubelet crash-loops the container (stuck 2/3 ready, repeated "failed startup probe" / "Killing"). The app still answers `GET / 200` (WildFly is up; only the WAR failed), so liveness looks fine — misleading.

Real case: `MaterializeMigrationExecutor` used `@Inject ManagedExecutorService executor`, which broke the sprint-156 deploy. Fix = swap to `@Resource`.
