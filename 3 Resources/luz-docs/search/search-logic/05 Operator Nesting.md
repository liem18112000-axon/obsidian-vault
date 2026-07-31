---
ai_hash: f52fa55c41984a48
ai_model: google/gemini-2.5-flash
ai_updated: '2026-05-30'
entities:
- Operator Nesting
- Query Operators
- and
- or
- not
- range
- recursive dispatch
- buildQueryBaseOnExpression()
- buildOperatorQuery()
- JSON tree
- nested nodes
- regexp
- term
- condition
- $and
- $or
- multi-term fulltext search
- documents
- search term
- Mobiliar
- '2021'
- documentTitle
- senderName
- standard fulltext-search shape
- reading complexity
- 04 Query Operators
- 06 Server Filters
- search-logic
---

# 05 — Operator Nesting

## The idea

Every operator from [[04 Query Operators]] is a building block. You can put any operator inside any other operator — `and` inside `or`, `or` inside `and`, `not` wrapping a `range`, and so on, as deep as you want.

This is called [[Glossary#Recursive dispatch|recursive dispatch]] in code. The functions `buildQueryBaseOnExpression()` and `buildOperatorQuery()` walk your JSON tree node by node and call themselves on nested nodes.

## Mental model

```
                ┌─────────┐
                │   and   │
                └────┬────┘
            ┌───────┴───────┐
        ┌───┴──┐        ┌───┴──┐
        │  or  │        │  or  │
        └───┬──┘        └───┬──┘
       ┌───┴───┐         ┌──┴────┐
   regexp1  regexp2   regexp3  regexp4
```

Each leaf is a real condition (`regexp`, `term`, …). Each branch is `and` or `or`. The translator walks bottom-up, converting each leaf to its MongoDB equivalent, then wrapping branches in `$and` / `$or`.

## Worked example — multi-term fulltext search

Goal: find documents where each search term ("Mobiliar", "2021") appears in **at least one** of the fields `documentTitle` or `senderName`.

```json
{
  "and": [
    { "or": [
        { "regexp": { "documentTitle": ".*Mobiliar.*" } },
        { "regexp": { "senderName":    ".*Mobiliar.*" } }
    ]},
    { "or": [
        { "regexp": { "documentTitle": ".*2021.*" } },
        { "regexp": { "senderName":    ".*2021.*" } }
    ]}
  ]
}
```

Reading it:
- Outer `and`: both terms must be present.
- Each inner `or`: the term can appear in *either* field.

This pattern (one `or`-of-fields per search term, all wrapped in an `and`) is how you implement "must contain all words, each in at least one field" — the standard fulltext-search shape.

## Rules of thumb

- Use `and` to **require** multiple conditions.
- Use `or` to **broaden** — any of these is fine.
- Use `not` to **exclude**.
- Deep nesting works, but each level adds reading complexity. Consider whether two nested `and`s can be flattened into one.

---

**Navigation:** [[04 Query Operators|← prev]] · [[search-logic|↑ index]] · [[06 Server Filters|next →]]

%% ai-graph-start %%

**Related notes:**
- [[04 Query Operators]]
- [[09 Examples]]
- [[search-logic]]
- [[03 Request Body]]
- [[Glossary]]

**Relations:**
- Operator Nesting — *uses* — Query Operators
- and — *is a type of* — Query Operators
- or — *is a type of* — Query Operators
- not — *is a type of* — Query Operators
- range — *is a type of* — Query Operators
- Operator Nesting — *is implemented by* — recursive dispatch
- recursive dispatch — *uses function* — buildQueryBaseOnExpression()
- recursive dispatch — *uses function* — buildOperatorQuery()
- buildQueryBaseOnExpression() — *processes* — JSON tree
- buildOperatorQuery() — *processes* — JSON tree
- JSON tree — *contains* — nested nodes
- regexp — *is a* — condition
- term — *is a* — condition
- and — *maps to* — $and
- or — *maps to* — $or
- multi-term fulltext search — *is an application of* — Operator Nesting
- multi-term fulltext search — *finds* — documents
- multi-term fulltext search — *involves* — search term
- Mobiliar — *is an example of* — search term
- 2021 — *is an example of* — search term
- documentTitle — *is a field for* — search term
- senderName — *is a field for* — search term
- multi-term fulltext search — *produces* — standard fulltext-search shape
- and — *requires* — multiple conditions
- or — *broadens* — conditions
- not — *excludes* — conditions
- Operator Nesting — *can lead to* — reading complexity
- 05 Operator Nesting — *follows* — 04 Query Operators
- 05 Operator Nesting — *precedes* — 06 Server Filters
- 05 Operator Nesting — *is part of* — search-logic

%% ai-graph-end %%