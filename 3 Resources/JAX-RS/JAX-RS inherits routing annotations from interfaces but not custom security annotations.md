---
title: "JAX-RS inherits routing annotations from interfaces but not custom security annotations"
created: 2026-08-24
type: lesson
status: seedling
source: "session 2026-08-24"
tags: [jax-rs, security, gotcha, refactoring]
---

# JAX-RS inherits routing annotations from interfaces but not custom security annotations

When extracting a JAX-RS resource into an interface + implementation, be deliberate about which annotations move. Per the JAX-RS spec, routing annotations (@Path, @GET, @POST, and parameter annotations) declared on an interface ARE inherited by the implementing class. But custom security/CDI annotations are generally NOT inherited from an interface, because reflective getAnnotation does not traverse interfaces. A filter/interceptor that reads e.g. axonivy @PermissionAllowed off the concrete class would silently fail-open if the annotation lived only on the interface.

Safe pattern: keep the extracted interface signatures-only and leave every annotation on the concrete resource class. That is how JsonStoreMongoDbApi was extracted in luz_jsonstore (both the v1 and v2 resources implement it, each keeping its own annotations).

## Related

- [[Serving a custom application/bson media type in JAX-RS via MessageBodyReader and Writer]]
