---
ai_hash: da9ec6e798ad0b73
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
created: 2026-05-28 09:17:32+07:00
entities:
- Divide-and-Conquer Count
- Divide-and-Conquer Visible-Document Count
- Database
- Index
- Performance Tuning
- User
- Document
- CPU Core
- _id space
- Range
- Parallel Counting
- Security Code
- Collection
- Field
- _id (field)
- MongoDB
- ObjectId
- Hexadecimal
- Visibility
- _isPublic (field)
- _effectiveSecurityClassCodes (field)
- Count Query
- Index Key
- Ascending Index
- Compound Index
- Multikey Index
- Array Field
- Amplification Factor
- De-duplication
- Latency
- Tail Latency
- Thread
- Concurrency
- Parallelism
- Divide and Conquer (pattern)
- Problem
- Sub-problem
- Partition
- K (number of partitions)
- Boundary
- Inclusive Bounds
- Exclusive Bounds
- Exact Count
- Fan-out
- Fan-in
- Wall-clock time
- CPU-bound
- I/O-bound
- Resident in memory
- Working Set
- Cache Hit
- Cache Miss
- Query Explain Plan
- Seek
- Scan
- Post-filter
- Stored Record
- Data Structure
- Processing Unit
- Path of Execution
- Duplicates
- Request time
- Precise result
- Multiple sub-requests
- Results
- User waiting time
- Query execution strategy
- Index Scan (IXSCAN)
- Collection Scan (COLLSCAN)
- Specific index part
- Many index entries
- Broader result set
source_note: '[[Kepler/Feature Notes/luz-docs/materialize/count-optimize/Divide-and-Conquer
  Visible-Document Count.md]]'
tags:
- luz-docs
- materialize
- count-optimization
- mongodb
- beginner
type: technical-explainer
---

# Divide-and-Conquer Count — Technical Points for Beginners

This note explains the technical terms behind [[Kepler/Feature Notes/luz-docs/materialize/count-optimize/Divide-and-Conquer Visible-Document Count.md]] for someone who is new to databases, indexes, and performance tuning.

Short version:

> We need to count how many documents a user can see. For some users this count is slow. Instead of doing one huge count on one CPU core, we split the `_id` space into several non-overlapping ranges, count each range in parallel, and add the results.

---

## 1. The business problem

The app stores documents. A user can see a document when:

```text
document is public
OR
document has at least one security code that the user has
```

So the system wants a number:

```text
How many documents can this user see?
```

For normal users this is fast. For "heavy users" it can be slow because they may have many security codes and those codes may match many documents.

---

## 2. Beginner glossary

### Document

A **document** is one stored record.

Example:

```json
{
  "_id": "66581234567890abcdef1234",
  "title": "Project Plan",
  "_isPublic": false,
  "_effectiveSecurityClassCodes": ["FINANCE", "LEGAL"]
}
```

Relational database analogy: a document is similar to a table row.

### Collection

A **collection** is a group of documents.

| Relational database | Document database |
|---|---|
| table | collection |
| row | document |
| column | field |

### Field

A **field** is a named value inside a document, like `_id`, `_isPublic`, or `_effectiveSecurityClassCodes`.

### `_id`

`_id` is the main identifier of a document. In MongoDB it is often an **ObjectId**.

Why `_id` matters:

- every document has one;
- it is unique;
- it can be sorted;
- it can be used to split documents into ranges.

### ObjectId

An **ObjectId** is a common MongoDB id type. It is 12 bytes, normally displayed as 24 hexadecimal characters.

Example:

```text
66581234567890abcdef1234
```

Beginner model:

> An ObjectId is like a long unique number that can be sorted.

Because 12 bytes = 96 bits, the theoretical ObjectId value space is approximately:

```text
0 to 2^96 - 1
```

Important: ObjectIds contain timestamp-related information, so real ObjectIds often cluster by creation time instead of being evenly spread.

### Hex / hexadecimal

**Hexadecimal** is base-16 counting:

```text
0 1 2 3 4 5 6 7 8 9 a b c d e f
```

A 24-character ObjectId is usually written in hex.

### Visibility

**Visibility** means whether a user is allowed to see a document.

In this feature:

```text
visible = _isPublic == true OR document security codes intersect user security codes
```

### `_isPublic`

`_isPublic` is a boolean field:

- `true`: everyone can see the document;
- `false`: visibility depends on security codes.

### `_effectiveSecurityClassCodes`

`_effectiveSecurityClassCodes` is likely an array of security labels.

Example:

```json
{
  "_effectiveSecurityClassCodes": ["FINANCE", "LEGAL", "CONFIDENTIAL"]
}
```

If the user has `LEGAL`, this document is visible to them.

### Intersects

Two sets **intersect** when they share at least one value.

```text
document codes = [FINANCE, LEGAL]
user codes     = [HR, LEGAL]
```

They intersect because both contain `LEGAL`.

---

## 3. Count query

A **count query** asks:

> How many documents match this condition?

Example:

```js
db.documents.countDocuments({ _isPublic: true })
```

The answer is just one number, but the database may still need to do a lot of work to compute it.

Counting is cheap when the database can use a good index and touch only a small amount of data.

Counting is expensive when:

- many documents match;
- array fields create many index entries;
- the same document is found multiple times;
- the database must de-duplicate results;
- the index does not match the query shape;
- the needed index pages are not in memory.

---

## 4. Index

An **index** is a data structure that helps the database find data faster.

Book analogy:

- without an index: read every page;
- with an index: jump to the relevant section.

Database analogy:

```text
No index -> scan many documents
Index    -> seek to the matching part of a sorted structure
```

Indexes improve reads, but cost storage and make writes more expensive.

### Index key

An **index key** is the field or fields used to build an index.

Example:

```js
{ _isPublic: 1 }
```

### Ascending index: `1`

In MongoDB index definitions:

```js
{ fieldName: 1 }
```

means ascending order.

### Compound index

A **compound index** uses more than one field.

Example:

```js
{ _effectiveSecurityClassCodes: 1, _id: 1 }
```

Mental model:

> A compound index is like a phone book sorted by last name first, then first name.

Field order matters.

For this optimization, useful index shapes are:

```js
{ _effectiveSecurityClassCodes: 1, _id: 1 }
{ _isPublic: 1, _id: 1 }
```

Why:

1. the first field narrows to the visibility condition;
2. `_id` as the second field lets each sub-query stay inside its `_id` slice.

### Multikey index

A **multikey index** is what MongoDB uses when indexing an array field.

Example document:

```json
{
  "_id": "docA",
  "_effectiveSecurityClassCodes": ["FINANCE", "LEGAL"]
}
```

An index on `_effectiveSecurityClassCodes` may contain separate entries:

```text
FINANCE -> docA
LEGAL   -> docA
```

This is useful because the database can find `docA` by either code.

But it can also create extra work.

---

## 5. Amplification factor

**Amplification** means the database does more internal work than the final answer suggests.

Example:

```text
1,000 visible documents
5 matching security codes per document
≈ 5,000 matching index entries to inspect
```

The answer is still 1,000 unique documents, but the database may process many more index entries.

That multiplier is the **amplification factor**.

---

## 6. De-duplication

**De-duplication** means removing duplicates.

Because a multikey index can have multiple entries for the same document, the database may encounter the same document more than once:

```text
FINANCE -> docA
LEGAL   -> docA
```

If the user has both `FINANCE` and `LEGAL`, `docA` must still be counted once.

---

## 7. Tail latency and p99

**Latency** is how long one request takes.

**Tail latency** means the slow edge of request times.

Example:

```text
p50 = 40 ms
p95 = 200 ms
p99 = 2,000 ms
```

**p99** means 99% of requests are faster than this value and 1% are slower.

This optimization targets the slow tail, not necessarily the average request.

---

## 8. CPU core, thread, concurrency, parallelism

### CPU core

A **CPU core** is a processing unit. A machine with 8 cores can often do 8 CPU-heavy tasks at the same time.

### Thread

A **thread** is one path of execution inside a process.

### Concurrency

**Concurrency** means multiple tasks are in progress during the same time period.

### Parallelism

**Parallelism** means multiple tasks are literally running at the same time, usually on different CPU cores.

This design wants:

```text
Thread 1 -> count range 0
Thread 2 -> count range 1
Thread 3 -> count range 2
Thread 4 -> count range 3
```

Then the partial counts are summed.

---

## 9. Divide and conquer

**Divide and conquer** is a classic pattern:

1. divide one big problem into smaller independent problems;
2. solve the smaller problems;
3. combine the answers.

Here:

| Step | Meaning |
|---|---|
| Divide | split `_id` space into K ranges |
| Solve | count each range separately |
| Combine | sum partial counts |

Formula:

```text
total = count(range_0) + count(range_1) + ... + count(range_K-1)
```

---

## 10. Partition, K, and boundaries

### Partition

A **partition** is one piece of a larger whole. Here, one partition is one `_id` range.

### K

`K` is the number of partitions.

```text
K = 1 -> no fan-out
K = 4 -> four sub-counts
K = 8 -> eight sub-counts
```

### Boundary

A **boundary** is a cut point between ranges.

For K = 4:

```text
0 ---- B1 ---- B2 ---- B3 ---- max
```

Ranges:

```text
R0: _id < B1
R1: B1 <= _id < B2
R2: B2 <= _id < B3
R3: _id >= B3
```

---

## 11. Inclusive and exclusive bounds

**Inclusive** means the boundary value is included:

```text
_id >= B1
```

**Exclusive** means the boundary value is not included:

```text
_id < B2
```

The safe range pattern is:

```text
B1 <= _id < B2
```

Why? No overlaps and no gaps.

Bad overlap:

```text
R0: _id <= B1
R1: _id >= B1
```

If `_id == B1`, it is counted twice.

Bad gap:

```text
R0: _id < B1
R1: _id > B1
```

If `_id == B1`, it is counted zero times.

---

## 12. Exact count

An **exact count** is the real answer, not an estimate.

This design is exact because:

- every document belongs to exactly one `_id` range;
- every range is counted;
- the final answer is the sum of all range counts.

```text
count(all) = count(R0) + count(R1) + count(R2) + count(R3)
```

---

## 13. Fan-out and fan-in

### Fan-out

**Fan-out** means one request becomes many sub-requests.

```text
one count request -> K count requests
```

### Fan-in / converge

After fan-out, the results are combined:

```text
partial counts -> sum -> final count
```

Fan-out can reduce wall-clock latency, but it increases database load.

---

## 14. Wall-clock time

**Wall-clock time** is the actual time the user waits.

Example:

```text
one count takes 8 seconds
```

If split into 4 equal sub-counts that run in parallel:

```text
each sub-count takes about 2 seconds
user waits about 2 seconds plus overhead
```

Total CPU work may still be around 8 CPU-seconds. The win is lower user-facing wait time.

---

## 15. CPU-bound vs I/O-bound

### CPU-bound

A task is **CPU-bound** when CPU computation is the bottleneck.

Fan-out can help if spare cores exist.

### I/O-bound

A task is **I/O-bound** when disk, network, or memory loading is the bottleneck.

Fan-out may make I/O-bound work worse because more tasks compete for the same I/O resources.

---

## 16. Resident in memory, working set, and cache hit ratio

An index is **resident in memory** when the database can read it from RAM instead of disk.

The **working set** is the data and indexes the database needs frequently.

A **cache hit** means the needed data was already in memory. A **cache miss** means it had to be fetched from slower storage.

Before increasing K, check whether the database is CPU-bound or waiting on cache misses / disk I/O.

---

## 17. Query explain plan

A **query explain plan** shows how the database plans to execute a query.

It helps answer:

- Did the query use an index?
- Which index?
- How many index keys were examined?
- How many documents were examined?
- Did the query scan the whole collection?

Common clues:

| Explain clue | Meaning |
|---|---|
| `IXSCAN` | index scan |
| `COLLSCAN` | collection scan |
| high keys examined | many index entries scanned |
| high docs examined | many documents fetched |

Use explain plans to verify that the `_id` range is actually being used by the index.

---

## 18. Seek vs scan

### Seek

A **seek** jumps to a specific part of an index.

### Scan

A **scan** reads many entries.

The goal is:

```text
seek to security code
then seek/limit to the _id range inside that code
```

Not:

```text
scan all matching security-code entries
then throw away documents outside the _id range
```

---

## 19. Post-filter

A **post-filter** means the database reads a broader set first and filters afterward.

Bad:

```text
1. Read all documents matching security code.
2. Discard documents outside the _id range.
```

Good:

```text
1. Use index to find matching security code.
2. Use _id in the same index to read only the needed range.
```

If `_id` is missing from the index, the `_id` condition may become a post-filter. Then K sub-counts can repeat lots of work.

---

## 20. Why `_id` should be second in the index

Recommended:

```js
{ _effectiveSecurityClassCodes: 1, _id: 1 }
{ _isPublic: 1, _id: 1 }
```

This means:

1. first narrow by security code or public flag;
2. then narrow by `_id` range inside that group.

This follows the common equality-then-range idea:

```text
equality field first, range field second
```

---

## 21. `$gte`, `$gt`, `$lte`, `$lt`

MongoDB-style range operators:

| Operator | Meaning |
|---|---|
| `$gte` | greater than or equal |
| `$gt` | greater than |
| `$lte` | less than or equal |
| `$lt` | less than |

Typical sub-range:

```js
{
  _id: {
    $gte: lowerBound,
    $lt: upperBound
  }
}
```

---

## 22. OR query

The visibility filter has an `OR`:

```text
_isPublic == true OR security code matches user
```

`OR` queries can be harder to optimize because the database may need to combine results from multiple branches.

Indexes should support both branches:

```js
{ _isPublic: 1, _id: 1 }
{ _effectiveSecurityClassCodes: 1, _id: 1 }
```

---

## 23. Security-sensitive count

This number is security-related.

If the system over-counts, it may reveal that documents exist.

If the system under-counts, it may hide documents that should be visible.

Either way, incorrect security-related counts are risky.

---

## 24. Fail loud

**Fail loud** means return an error instead of pretending an incomplete result is valid.

For fan-out count:

```text
If any sub-count fails, the whole count fails.
```

Bad:

```text
R0 succeeded = 100
R1 failed    = unknown
R2 succeeded = 200
wrong total  = 300
```

Better:

```text
Count failed; retry or show an error.
```

---

## 25. Safe default and configuration

The config is:

```text
luz.docs.materialize.count-fanout-partitions
```

Meaning:

```text
K <= 1 -> fan-out off
K > 1  -> split into K sub-counts
```

Safe defaults matter because fan-out increases database load.

---

## 26. Materialised read path

A **materialised read path** usually means the system has prepared a read-optimized model of data.

The app is not calculating everything from raw source events every time. It uses a stored/read-friendly representation.

This visible-document count lives on that read path.

---

## 27. Storage gateway

A **storage gateway** is an application layer that hides database details from the rest of the system.

It may handle:

- converting string ids to ObjectIds;
- choosing read preferences;
- applying query conventions;
- centralizing database access logic.

---

## 28. Type coercion and Extended JSON

**Type coercion** means converting one type into another.

Example:

```text
"66581234567890abcdef1234" -> ObjectId("66581234567890abcdef1234")
```

**Extended JSON** is a JSON representation for special BSON types.

Example:

```json
{ "$oid": "66581234567890abcdef1234" }
```

The source note says the gateway already coerces 24-character hex strings to ObjectIds, so special Extended JSON wrappers are not needed. This should be verified because string comparison and ObjectId comparison are not the same thing.

---

## 29. Replica set, read preference, analytics replica

### Replica set

A **replica set** is a group of database servers with copies of the same data.

Typical roles:

- primary: handles writes;
- secondary: replicates data and may serve reads depending on configuration.

### Read preference

**Read preference** controls which replica receives read operations.

Heavy counts might be routed to a secondary or analytics replica to avoid disturbing the primary.

### Analytics replica

An **analytics replica** is a copy intended for heavy reporting/query workloads.

Trade-off: secondary reads may have different consistency behavior and require storage-gateway support.

---

## 30. Load skew

**Load skew** means work is unevenly distributed.

Example:

```text
R0: 1,000 documents
R1: 1,000 documents
R2: 1,000 documents
R3: 90,000 documents
```

The final runtime is controlled by the slowest range.

ObjectIds often cluster by creation time, so uniform ObjectId ranges may not produce equal-sized buckets.

---

## 31. Uniform boundaries vs quantile boundaries

### Uniform boundaries

Split the theoretical ObjectId number space evenly.

Pros:

- easy;
- no sampling;
- deterministic.

Cons:

- actual data may not be evenly distributed.

### Quantile boundaries

Split based on actual data distribution.

Example:

```text
R0: 25% of actual documents
R1: 25% of actual documents
R2: 25% of actual documents
R3: 25% of actual documents
```

Pros:

- less skew.

Cons:

- more complex;
- requires sampling/statistics;
- boundaries can become stale.

---

## 32. Contention and connection pool

**Contention** means many tasks compete for the same limited resource:

- CPU;
- disk;
- memory bandwidth;
- database connections;
- locks.

A **connection pool** is a reusable set of database connections.

Fan-out uses more simultaneous database operations. If K is too high, the system can overload the database or exhaust the connection pool.

---

## 33. Timeouts, retries, and concurrency limits

Production fan-out needs guardrails:

- max K;
- timeout per sub-query;
- bounded thread pool;
- max number of fan-out counts running at once;
- careful retries with backoff;
- never return partial totals as complete totals.

Retries can help temporary failures, but they also multiply load.

---

## 34. Observability

**Observability** means having enough metrics/logs/traces to understand the system.

Track:

- total count latency;
- latency per partition;
- number of partitions K;
- keys examined;
- docs examined;
- errors per partition;
- timeouts;
- database CPU;
- cache hit ratio;
- connection pool usage;
- p95/p99 latency before and after.

Without metrics, tuning K is guessing.

---

## 35. Bitmap, Roaring bitmap, HyperLogLog, cardinality

### Cardinality

**Cardinality** means number of unique items in a set.

Visible-document count is a cardinality problem:

```text
How many unique documents can the user see?
```

### Bitmap

A **bitmap** stores yes/no membership using bits.

Conceptually:

```text
bit 0 -> document 0 visible?
bit 1 -> document 1 visible?
bit 2 -> document 2 visible?
```

### Roaring bitmap

A **Roaring bitmap** is a compressed bitmap format for storing sets of integers efficiently and doing fast set operations.

Possible future approach:

```text
visible docs = publicDocs OR docsForCodeA OR docsForCodeB OR docsForCodeC
```

This can reduce multikey amplification, but it requires more architecture, storage, and update logic.

### HyperLogLog

**HyperLogLog** estimates the number of unique items using small memory.

It is approximate, not exact.

It can be useful for analytics, but it is not a direct replacement if the product requires exact security-visible counts.

---

## 36. Tiny example

Pretend `_id` is just a number from 0 to 99.

K = 4.

Boundaries:

```text
B1 = 25
B2 = 50
B3 = 75
```

Ranges:

```text
R0: _id < 25
R1: 25 <= _id < 50
R2: 50 <= _id < 75
R3: _id >= 75
```

Visible documents:

```text
10, 26, 27, 70, 90
```

Partial counts:

```text
R0: 1  (10)
R1: 2  (26, 27)
R2: 1  (70)
R3: 1  (90)
```

Final count:

```text
1 + 2 + 1 + 1 = 5
```

Same answer as one big count, but the work can be shared.

---

## 37. Conceptual implementation

### Step 1 — Read config

```text
K = luz.docs.materialize.count-fanout-partitions
```

If K <= 1, use normal count.

### Step 2 — Build boundaries

```text
space = 2^96
B1 = space * 1 / K
B2 = space * 2 / K
...
```

Convert each boundary to a 24-character hex string.

### Step 3 — Build sub-queries

Each sub-query contains:

- original visibility filter;
- lower `_id` bound if not first range;
- upper `_id` bound if not last range.

### Step 4 — Run concurrently

Submit sub-counts to a bounded thread pool.

### Step 5 — Fail if any sub-query fails

Do not return partial totals.

### Step 6 — Sum results

Return the exact total.

---

## 38. Verification checklist

### Correctness

- [ ] Ranges have no gaps.
- [ ] Ranges have no overlaps.
- [ ] Every sub-query includes the same visibility filter.
- [ ] Any sub-query failure fails the whole count.
- [ ] `_id` strings are correctly coerced to ObjectIds.

### Indexes

- [ ] `{ _effectiveSecurityClassCodes: 1, _id: 1 }` exists.
- [ ] `{ _isPublic: 1, _id: 1 }` exists.
- [ ] Explain plan uses indexes.
- [ ] `_id` range is applied inside the index scan, not only as post-filter.

### Performance

- [ ] Count is CPU-bound, not I/O-bound.
- [ ] Database has spare CPU cores.
- [ ] Index working set is mostly in memory.
- [ ] K does not exceed safe concurrency.
- [ ] Connection pool can handle fan-out.
- [ ] p99 improves in load testing.

### Operations

- [ ] Metrics exist per partition.
- [ ] Timeouts exist.
- [ ] Rollback is easy: set K <= 1.
- [ ] Alerts exist for error spikes.

---

## 39. Common mistakes

### Mistake 1 — Overlapping ranges

```text
R0: _id <= B1
R1: _id >= B1
```

If `_id == B1`, it is counted twice.

### Mistake 2 — Gaps between ranges

```text
R0: _id < B1
R1: _id > B1
```

If `_id == B1`, it is not counted.

### Mistake 3 — Missing `_id` in the compound index

Bad:

```js
{ _effectiveSecurityClassCodes: 1 }
```

Better:

```js
{ _effectiveSecurityClassCodes: 1, _id: 1 }
```

### Mistake 4 — Setting K too high

Fan-out is not free. Too many sub-counts can overload CPU, disk, memory, or connection pools.

### Mistake 5 — Ignoring skew

Uniform ObjectId ranges do not guarantee equal work. Measure per-partition latency.

---

## 40. One-line mental model

> Cut the document id number line into K non-overlapping slices, count each slice at the same time using compound indexes, and add the results. It is exact, but it only improves latency when the database has spare CPU and the indexes support the slice queries.

---

## 41. Internet references used

- MongoDB Manual — ObjectId: https://www.mongodb.com/docs/manual/reference/bson-types/#objectid
- MongoDB Manual — Indexes: https://www.mongodb.com/docs/manual/indexes/
- MongoDB Manual — Compound indexes: https://www.mongodb.com/docs/manual/core/indexes/index-types/index-compound/
- MongoDB Manual — Multikey indexes: https://www.mongodb.com/docs/manual/core/indexes/index-types/index-multikey/
- MongoDB Manual — `countDocuments()`: https://www.mongodb.com/docs/manual/reference/method/db.collection.countDocuments/
- MongoDB Manual — Explain results: https://www.mongodb.com/docs/manual/reference/explain-results/
- MongoDB Manual — Equality, Sort, Range guideline: https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-guideline/
- MongoDB Manual — Read preference: https://www.mongodb.com/docs/manual/core/read-preference/
- Redis Docs — HyperLogLog: https://redis.io/docs/latest/develop/data-types/probabilistic/hyperloglogs/
- RoaringBitmap project: https://roaringbitmap.org/

%% ai-graph-start %%

**Related notes:**
- [[Divide-and-Conquer Visible-Document Count]]
- [[Glossary]]
- [[07 Aggregation Pipeline]]
- [[index]]
- [[search-logic]]

**Relations:**
- Divide-and-Conquer Count — *explains* — Divide-and-Conquer Visible-Document Count
- Divide-and-Conquer Count — *explains concepts for* — Database
- Divide-and-Conquer Count — *explains concepts for* — Index
- Divide-and-Conquer Count — *explains concepts for* — Performance Tuning
- User — *can see* — Document
- Divide-and-Conquer Count — *splits* — _id space
- _id space — *is split into* — Range
- Range — *is counted using* — Parallel Counting
- Parallel Counting — *uses* — CPU Core
- Document — *is a* — Stored Record
- Collection — *is a group of* — Document
- Document — *has* — Field
- _id (field) — *is a* — Field
- _id (field) — *is main identifier for* — Document
- _id (field) — *is often an* — ObjectId
- MongoDB — *uses* — ObjectId
- ObjectId — *is displayed as* — Hexadecimal
- Visibility — *determines if* — User can see Document
- Document — *has* — _isPublic (field)
- Document — *has* — _effectiveSecurityClassCodes (field)
- _isPublic (field) — *influences* — Visibility
- _effectiveSecurityClassCodes (field) — *influences* — Visibility
- User — *has* — Security Code
- Count Query — *determines number of* — Document
- Index — *is a* — Data Structure
- Index — *improves performance for* — Database
- Index — *has* — Index Key
- Ascending Index — *is a type of* — Index
- Compound Index — *is a type of* — Index
- Multikey Index — *is a type of* — Index
- Multikey Index — *indexes* — Array Field
- Amplification Factor — *describes* — Database internal work
- De-duplication — *removes* — Duplicates
- Multikey Index — *can cause* — Duplicates
- Latency — *measures* — Request time
- Tail Latency — *is a type of* — Latency
- Divide-and-Conquer Count — *targets* — Tail Latency
- CPU Core — *is a* — Processing Unit
- Thread — *is a* — Path of Execution
- Concurrency — *involves* — Multiple tasks in progress
- Parallelism — *involves* — Multiple tasks running simultaneously
- Parallelism — *uses* — CPU Core
- Divide and Conquer (pattern) — *solves* — Problem
- Problem — *is divided into* — Sub-problem
- Divide-and-Conquer Count — *applies* — Divide and Conquer (pattern)
- Partition — *is a* — Range
- K (number of partitions) — *is the number of* — Partition
- Boundary — *defines* — Range
- Inclusive Bounds — *includes* — Boundary
- Exclusive Bounds — *excludes* — Boundary
- Exact Count — *is a* — Precise result
- Divide-and-Conquer Count — *provides* — Exact Count
- Fan-out — *creates* — Multiple sub-requests
- Fan-in — *combines* — Results
- Fan-in — *is also known as* — Converge
- Wall-clock time — *is* — User waiting time
- Fan-out — *can reduce* — Wall-clock time
- CPU-bound — *means* — CPU is bottleneck
- I/O-bound — *means* — I/O is bottleneck
- Index — *can be* — Resident in memory
- Working Set — *contains* — Frequently needed data and indexes
- Cache Hit — *means* — Data found in memory
- Cache Miss — *means* — Data fetched from slower storage
- Query Explain Plan — *shows* — Query execution strategy
- Query Explain Plan — *can indicate* — Index Scan (IXSCAN)
- Query Explain Plan — *can indicate* — Collection Scan (COLLSCAN)
- Seek — *accesses* — Specific index part
- Scan — *reads* — Many index entries
- Post-filter — *filters* — Broader result set
- _id (field) — *should be second in* — Compound Index
- Compound Index — *can start with* — _effectiveSecurityClassCodes (field)
- Compound Index — *can start with* — _isPublic (field)
- Range — *are* — Non-overlapping

%% ai-graph-end %%