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
