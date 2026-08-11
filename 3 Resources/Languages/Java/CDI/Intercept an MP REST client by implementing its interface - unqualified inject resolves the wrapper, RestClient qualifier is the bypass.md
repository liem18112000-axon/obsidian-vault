---
ai_hash: 8f48556b9216e695
ai_model: google/gemini-2.5-flash
ai_updated: '2026-07-31'
created: 2026-06-05
entities: []
source: TrackingJsonStoreClient refactor, session 2026-06-05
status: budding
tags:
- java
- cdi
- microprofile
- rest-client
- interceptor
- luz-docs
title: Intercept an MP REST client by implementing its interface - unqualified inject
  resolves the wrapper, RestClient qualifier is the bypass
type: howto
---

# Intercept an MP REST client by implementing its interface - unqualified inject resolves the wrapper, RestClient qualifier is the bypass

To intercept every call to a MicroProfile REST client (e.g. for change tracking), make the wrapper bean **implement the client interface** instead of being a parallel facade type:

```java
@ApplicationScoped
public class TrackingJsonStoreClient implements JsonStoreMongoClient {
    @Inject @RestClient private JsonStoreMongoClient client; // raw delegate
    ...
}
```

CDI resolution does the routing: the MP-generated proxy bean carries the `@RestClient` qualifier, the wrapper carries `@Default` — so a plain `@Inject JsonStoreMongoClient` gets the wrapper, and bypassing it requires writing `@RestClient` explicitly (greppable, intentional). Two extra properties:

1. **Compiler-enforced coverage** — adding a method to the client interface breaks the build until the wrapper implements it; a facade silently misses new methods.
2. **No server-side registration risk** — the client interface carries `@Path`/`@PUT`, but JAX-RS spec 3.6 does NOT inherit class-level annotations, so the implementing bean is never published as a REST resource.

Preferred over a CDI `@AroundInvoke` interceptor bound on the client interface: that route is spec-allowed but depends on the implementation honoring bindings on synthetic rest-client beans, and degrades to method-name dispatch + positional `Object[]` argument extraction. A CDI `@Decorator` does not reliably apply to rest-client proxies at all.

Applied in luz_docs (WildFly 26, javax), commit 0ec298177: JsonStoreMongoService injects the interface unqualified. Three sites (MigrationCampaignService, MaterializeMigrationExecutor, GroupService) deliberately keep the raw `@RestClient` client — with this pattern that bypass is explicit and greppable rather than accidental.

## Related

- [[CDI observer methods are inherited from superclasses but not across packages if package-private]]
- [[luz_docs change tracking dropped the ChangeOrigin event marker - thread-local suppression is the loop guard]]

%% ai-graph-start %%

**Related notes:**
- [[CDI decorators and interceptors never fire on MicroProfile REST client proxies]]
- [[Implementing a @Path-annotated interface auto-registers the class as a JAX-RS server resource]]
- [[luz_docs JsonStore change tracking via client-layer wrapper and CDI async events]]
- [[luz_docs tracked-write template folds the four single-doc ops into one gate-preread-write-fire method]]
- [[Mockito @InjectMocks by type stale @Mock after @RestClient swap leaves real field null]]

%% ai-graph-end %%