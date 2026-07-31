---
title: "Mockito @InjectMocks by type: stale @Mock after @RestClient swap leaves real field null"
created: 2026-07-15
type: lesson
status: seedling
source: "session 2026-07-15 LUZ-156856"
tags: [mockito, java, gotcha, luz-docs, testing]
---

# Mockito @InjectMocks by type: stale @Mock after @RestClient swap leaves real field null

When you swap a CDI collaborator from an injected service to a `@RestClient` client (e.g. `JsonStoreMongoService` → `@RestClient JsonStoreMongoClient mdb`), update the unit test to `@Mock` the **new** type. Mockito `@InjectMocks` wires mocks **by type**, so a leftover `@Mock` of the old type is silently ignored and the real `@RestClient` field stays `null`.

The symptom is misleading: a runtime `NullPointerException` like `Cannot invoke "...JsonStoreMongoClient.find(...)" because "this.mdb" is null` — it looks like a mock stub miss, but the field was never injected at all.

Fix: mock the actually-injected type and stub the JAX-RS `Response` the client returns:

```java
@Mock JsonStoreMongoClient mdb;   // not the old service
...
Response response = mock(Response.class);
when(response.readEntity(String.class)).thenReturn(batch.toString());
when(mdb.find(any(), any(), any(), eq(TENANT), eq(DOCUMENT_COLLECTION), any(), any(), anyInt(), anyInt(), any(), any()))
        .thenReturn(response);
```

The executor parses the response body itself (`arrayOf(res)` → `readEntity(String.class)` → `Json.createReader(...).readArray()`), so the mock only needs to feed the JSON string. Try-with-resources on a `null` Response is safe (Java guards null before `close()`), so `updateOne` returning a default `null` mock does not NPE.

Context: luz_docs `ParallelizeMigrationExecutorTest`, LUZ-156856 — test broke after PR #1361 reworked the executor from `jsonStore.getCollectionsByFilter` to `mdb.find`/`mdb.updateOne`.

## Related

- [[luz-docs]]
