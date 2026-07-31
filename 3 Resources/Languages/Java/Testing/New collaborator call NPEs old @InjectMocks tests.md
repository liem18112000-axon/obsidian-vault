---
ai_hash: 02de12f2ca83adfd
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-04
entities: []
source: session 2026-06-04 LUZ-155107
status: seedling
tags:
- mockito
- java
- gotcha
title: New collaborator call NPEs old @InjectMocks tests
type: lesson
---

# New collaborator call NPEs old @InjectMocks tests

Adding a call to a new injected collaborator inside a service method breaks every existing unit test that builds the service with @InjectMocks but does not declare a @Mock for that collaborator: Mockito leaves undeclared fields null, so the new call throws NPE.

**Fix:** add the @Mock field. Mockito's default stubbing (false/null/empty) usually preserves old test behavior — e.g. a new `shouldUseMaterialized(tenantId)` gate defaults to false, so legacy tests skip the new branch and stay green without further stubbing.

%% ai-graph-start %%

**Related notes:**
- [[A merged-in test breaks when the target branch's service gained a new injected dependency]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]
- [[Mockito strict stubs flag mismatched-arg calls on a stubbed method as failures]]
- [[mockStatic ConfigProvider without getConfig stub latches null into static Config fields]]
- [[MicroProfile Fallback is dead in plain Mockito unit tests]]

%% ai-graph-end %%