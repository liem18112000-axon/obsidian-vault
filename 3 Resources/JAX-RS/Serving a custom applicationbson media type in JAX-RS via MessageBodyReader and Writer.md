---
title: "Serving a custom application/bson media type in JAX-RS via MessageBodyReader and Writer"
created: 2026-08-24
type: howto
status: seedling
source: "session 2026-08-24"
tags: [jax-rs, bson, mongodb, microprofile]
---

# Serving a custom application/bson media type in JAX-RS via MessageBodyReader and Writer

A JAX-RS/MicroProfile service can speak a binary BSON wire format by registering two @Provider classes: a MessageBodyReader<Object> annotated @Consumes("application/bson") and a MessageBodyWriter<Object> annotated @Produces("application/bson"). Gate isReadable/isWriteable with MediaType.isCompatible against new MediaType("application","bson") plus the concrete Java types you support (e.g. Document, List, DTOs).

Encode/decode org.bson.Document with the mongo-java-driver codec:
- encode: DocumentCodec.encode(new BsonBinaryWriter(new BasicOutputBuffer()), doc, EncoderContext.builder().build()), then buffer.toByteArray().
- decode: DocumentCodec.decode(new BsonBinaryReader(ByteBuffer.wrap(bytes)), DecoderContext.builder().build()).

Read the raw request bytes with commons-io IOUtils.toByteArray(entityStream). In an app that relies on classpath scanning (its Application subclass declares no explicit classes/singletons), @Provider readers/writers are auto-discovered, so no manual registration is needed.

Seen in luz_jsonstore (Klara/AxonIvy) v2 endpoints; Java 8, mongo-java-driver 3.12.13 uber-jar that bundles org.bson.

## Related

- [[BSON has no top-level array so a document list must be wrapped in a document]]
- [[Use a BSON endpoint instead of JSON to store MongoDB dates as native Date]]
- [[JAX-RS inherits routing annotations from interfaces but not custom security annotations]]
