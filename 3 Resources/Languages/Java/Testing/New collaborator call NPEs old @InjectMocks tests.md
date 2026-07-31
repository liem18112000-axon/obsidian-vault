---
title: "New collaborator call NPEs old @InjectMocks tests"
created: 2026-06-04
type: lesson
status: seedling
source: "session 2026-06-04 LUZ-155107"
tags: [mockito, java, gotcha]
---

# New collaborator call NPEs old @InjectMocks tests

Adding a call to a new injected collaborator inside a service method breaks every existing unit test that builds the service with @InjectMocks but does not declare a @Mock for that collaborator: Mockito leaves undeclared fields null, so the new call throws NPE.

**Fix:** add the @Mock field. Mockito's default stubbing (false/null/empty) usually preserves old test behavior — e.g. a new `shouldUseMaterialized(tenantId)` gate defaults to false, so legacy tests skip the new branch and stay green without further stubbing.
