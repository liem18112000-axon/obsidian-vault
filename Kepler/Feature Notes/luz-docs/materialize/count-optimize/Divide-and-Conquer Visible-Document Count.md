# Divide-and-Conquer Visible-Document Count

> Split one slow count into **K concurrent sub-counts over disjoint `_id` ranges**, then sum the partials.

[[divide-conquer-count.excalidraw|Overall Diagram]]
[[divide-conquer-count.png|Raw Picture]]

---

## 1. The problem

![[s1-problem.png]]

For the materialised read path, a user's visible-document total is a single count:

```
count of documents where
      _isPublic == true
   OR _effectiveSecurityClassCodes  intersects  user.codes
```

On a multikey index this is cheap for most users. It becomes a tail-latency problem for **heavy users** — many security codes, where each code covers many documents, and individual documents carry several of those codes.

Why it spikes: the engine does not pay for the *answer*, it pays for every `(document × matching-code)` index entry and then de-duplicates them by record id. So the work scales with that **amplification factor**, not with the distinct-document number you actually want. One request, one thread, one CPU core — while the other cores sit idle. That is the p99 spike.

---

## 2. The idea

![[s2-partition.png]]

Same total work, but spread across cores instead of piled onto one.

Cut the space of document `_id`s into **K contiguous, non-overlapping ranges**. Count each range separately and concurrently. Add the partial counts together.

```
total = count(filter AND _id in range_0)
      + count(filter AND _id in range_1)
      + ...
      + count(filter AND _id in range_{K-1})
```

This is divide-and-conquer: the count is the problem, the `_id` ranges are the disjoint sub-problems, the sum is the combine step.

---

## 3. Partitioning the `_id` space

Each document `_id` is a 24-character hex ObjectId — effectively a number between `0` and `2⁹⁶`. To make K ranges, place `K − 1` evenly-spaced **boundaries** across that number line.

For K = 4 the boundaries land at the quarter marks:

```
0 ──────── B1 ──────── B2 ──────── B3 ──────── 2⁹⁶
   range R0    range R1    range R2    range R3
```

| Range | Condition          |
|-------|--------------------|
| R0    | `_id < B1`         |
| R1    | `B1 ≤ _id < B2`    |
| R2    | `B2 ≤ _id < B3`    |
| R3    | `_id ≥ B3`         |

Pseudo-code for the boundaries:

```
function rangeBounds(K):
    space = 2^96                 # one past the largest 24-hex ObjectId
    bounds = []
    for i in 1 .. K-1:
        cut = (space * i) / K    # the i-th evenly spaced point
        bounds.append(hex24(cut))   # 24-char zero-padded hex string
    return bounds                # K-1 interior boundaries
```

Each boundary is **shared by its two neighbours**: `B_i` is the *exclusive upper bound* of range `i` and the *inclusive lower bound* of range `i + 1`. The first range is open below, the last range is open above.

Result: the ranges **tile the whole space with no gap and no overlap**. Every document falls into exactly one range, so the sum of partial counts is always **exact** — no matter how the ids are distributed.

---

## 4. Fan-out: counting the ranges concurrently

![[s3-fanout.png]]

![[s4-converge.png]]

Each range becomes its own query: the original visibility filter **AND** an `_id` range clause.

```
function partition(baseFilter, K):
    bounds = rangeBounds(K)
    queries = []
    for i in 0 .. K-1:
        idClause = {}
        if i > 0:        idClause.lowerInclusive = bounds[i-1]
        if i < K-1:      idClause.upperExclusive = bounds[i]
        queries.append( baseFilter AND { _id within idClause } )
    return queries
```

A sub-count query looks like:

```
match:
    < base visibility filter >
    AND _id >= B1  AND _id < B2     # the slice for range R1
```

The K queries are submitted to a thread pool and run **at the same time**, one per core. As each returns its partial count, the results are summed:

```
function count(filter, K):
    if K <= 1:
        return singleCount(filter)            # fan-out disabled
    partials = runConcurrently(partition(filter, K), each -> singleCount(each))
    return sum(partials)
```

**Correctness guard — fail loud.** If *any* sub-count errors out, the whole count fails; it never returns a partial sum. A silently dropped partition would under-count, and an under-count on a *security visibility* number is an access-leak-shaped bug. Better to error than to quietly show too few documents.

---

## 5. Why each sub-count stays fast — the index

![[s5-index.png]]

Adding an `_id` range only helps if the database can *seek* to that slice instead of scanning everything and throwing away the rest. That requires the right index shape:

```
{ _effectiveSecurityClassCodes : 1 ,  _id : 1 }
{ _isPublic                    : 1 ,  _id : 1 }
```

The trick is `_id` as the **second** index key. With the security field first, the engine narrows to the matching security entries; with `_id` second, it can then **bound the `_id` range inside that matched interval**. So each sub-count walks only its own slice of index entries.

Without `_id` in the index, the range clause becomes a *post-filter*: scan all matching documents, discard the out-of-range ones. That is K× the work for zero speedup — the opposite of the goal.

---

## 6. A note on identity types

The range boundaries are passed as plain 24-character hex strings. The storage gateway already coerces an `_id` string into a real ObjectId for equality and "in-list" lookups, so the same coercion applies to the range bounds. No special extended-JSON wrapper is needed.

---

## 7. Configuration

Fan-out is controlled by a single setting — the number of partitions **K**:

```
luz.docs.materialize.count-fanout-partitions
```

- `≤ 1` → fan-out **off**: a normal single count (the safe default).
- `K`   → split into K concurrent sub-counts.

The entry point is the materialised total-count path; flipping the setting changes its behaviour with no other change required.

---

## 8. Trade-offs and limits

![[s8-skew.png]]

**Load skew.** An ObjectId begins with a creation timestamp, so real ids cluster by time rather than spreading evenly across the hex space. Uniform boundaries therefore produce **uneven buckets** — some sub-counts do more work than others. This never affects correctness (the sum stays exact); it only caps the speedup. If skew is severe, replace the evenly-spaced boundaries with **quantile boundaries** sampled from the actual data — that is the one place to change.

**No secondary routing.** Ideally the heavy sub-counts run on an analytics replica so they do not disturb everyone else's latency. The current count path has no read-preference knob, so routing to a secondary needs a change on the storage-gateway side.

**Only helps when CPU-bound.** Fan-out spreads work across cores; it does not reduce total work. It pays off only when the count is CPU-bound, there are spare cores, and the index is resident in memory. If the bottleneck is I/O (cold index pages faulting under memory pressure), running K sub-counts at once buys **contention, not speed** — fix cache sizing first. Confirm which regime you are in (query explain plan + cache hit ratio) before tuning K.

**Amplification still there.** This technique parallelises the amplified work; it does not remove it. The ceiling is the core count. For a persistent big-count tail, a bitmap-union approach (e.g. Roaring bitmaps for exact, HyperLogLog for approximate) removes the amplification entirely — a larger change, more machinery, decide separately.

---

## 9. The full picture in one line

> Cut the id space into K tiles → count each tile concurrently on its own core, each seeking its slice via the `{security, _id}` index → sum the exact partials. Wins wall-clock on CPU-bound heavy counts; correctness is independent of K and of data distribution.
