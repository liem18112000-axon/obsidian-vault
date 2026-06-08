---
title: "Records break Gson pre-2.10, ArangoDB driver 6 VPACK, and handlebars getter resolution"
created: 2026-06-07
type: lesson
status: seedling
source: "LEO CDP code-modernization planning, 2026-06-07"
tags: [java, records, gson, arangodb, handlebars, serialization, gotcha]
---

# Records break Gson pre-2.10, ArangoDB driver 6 VPACK, and handlebars getter resolution

Blanket 'convert POJOs to records' breaks serialization-heavy Java apps in three specific ways, all present in LEO CDP: (1) **Gson < 2.10 cannot deserialize records at all** (needs no-arg ctor / Unsafe field write; record support landed in 2.10) - bump Gson before any record crosses a JSON boundary; (2) **ArangoDB java-driver 6.x VPACK mapping needs no-arg ctor + setters** - persisted document entities must stay mutable classes until driver 7 (Jackson serde, record-capable); (3) **handlebars/mustache resolve {{name}} via getName()/field** - record accessors are name() and invisible to older resolvers, so template-rendered models stay classes. Plus a quieter trap: records define components-based equals/hashCode - converting a class used as a HashMap/HashSet key with identity semantics silently changes behavior. Triage rule: records ONLY for non-persisted, non-templated, immutable value types; everything else needs its enabling library bump first.

## Related

- [[Binary-only vendored jars pin the API level of everything they link]]
