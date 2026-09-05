---
title: "LRU cache in Java via LinkedHashMap accessOrder + removeEldestEntry"
created: 2026-08-26
type: howto
status: seedling
source: "session 2026-08-26 luz_jsonstore MongoClientFactory"
tags: [java, cache, lru, linkedhashmap, pattern]
---

# LRU cache in Java via LinkedHashMap accessOrder + removeEldestEntry

A fixed-capacity **LRU cache** falls out of `LinkedHashMap` almost for free:

```java
new LinkedHashMap<K,V>(capacity, 0.75f, /*accessOrder=*/true) {
    @Override protected boolean removeEldestEntry(Map.Entry<K,V> e) {
        return size() > MAX;   // evict least-recently-used once over cap
    }
};
```

- The third constructor arg `accessOrder=true` flips insertion-order to **access-order**: every `get`/`put` moves that entry to the tail (most-recently-used), so the *head* is the least-recently-used.
- `removeEldestEntry` is a **template-method hook** that `LinkedHashMap` calls after every insertion; return `true` and it drops the eldest (= LRU) entry.
- **Gotcha:** with `accessOrder=true` even a `get()` structurally mutates the map, so it is *not* thread-safe on its own — wrap it in `Collections.synchronizedMap(...)`.

Ref: baeldung.com/java-linked-hashmap.

## Related
[[Cache one MongoClient per tenant and close it on eviction]]

## Related

- [[Cache one MongoClient per tenant and close it on eviction]]
