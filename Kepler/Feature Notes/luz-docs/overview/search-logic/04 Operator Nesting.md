---
type: technical-section
topic: luz-docs search logic
level: beginner
---

# 04 Operator Nesting

Parent: [[Kepler/Feature Notes/luz-docs/overview/search-logic.md]]

Related:
- [[03 Query Operators]]
- [[07 Common Use Cases]]

---

## What is nesting?

**Nesting** means putting one query inside another query.

Example:

```json
{
  "and": [
    {
      "or": [
        { "regexp": { "documentTitle": ".*Mobiliar.*" } },
        { "regexp": { "senderName": ".*Mobiliar.*" } }
      ]
    },
    {
      "or": [
        { "regexp": { "documentTitle": ".*2021.*" } },
        { "regexp": { "senderName": ".*2021.*" } }
      ]
    }
  ]
}
```

Beginner translation:

```text
Document must match:
  (Mobiliar in title OR Mobiliar in sender)
AND
  (2021 in title OR 2021 in sender)
```

---

## Why nesting is useful

Nesting lets you build complex search behavior from simple pieces.

Example user search:

```text
Mobiliar 2021
```

The system may interpret this as:

```text
Every search word must appear somewhere useful.
```

For each word, search across several fields:

```text
Mobiliar can be in title OR sender
2021 can be in title OR sender
```

Then combine the words with `and`:

```text
Mobiliar condition AND 2021 condition
```

---

## Recursive dispatch

The source note mentions:

```text
buildQueryBaseOnExpression()
buildOperatorQuery()
```

Beginner meaning:

> The converter looks at the current operator. If it finds another query inside, it calls itself again.

This is called **recursion**.

Restaurant analogy:

```text
Order:
  Combo meal
    Burger
    Fries
    Drink
```

The waiter handles the combo by handling each smaller item.

Query analogy:

```text
and
  or
    regexp
    regexp
  or
    regexp
    regexp
```

The converter handles the `and`, then each `or`, then each `regexp`.

---

## How to read nested queries

Use indentation.

Bad mental view:

```text
giant JSON blob
```

Better mental view:

```text
AND
├── OR
│   ├── title contains Mobiliar
│   └── sender contains Mobiliar
└── OR
    ├── title contains 2021
    └── sender contains 2021
```

This means:

> One condition from the first OR must match, and one condition from the second OR must match.

---

## Common nesting patterns

### All words, any field

```text
AND over words
  OR over fields
```

Use this for multi-term fulltext-style search.

### Any selected filter

```text
OR over selected values
```

Use this for "status is A or B or C".

### Required filters

```text
AND over required conditions
```

Use this for "sender is X and date is in range and file size is below Y".

