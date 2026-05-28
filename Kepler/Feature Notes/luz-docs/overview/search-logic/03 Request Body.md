# 03 — Request Body Structure

The JSON body you send with every `POST /search` or `/count` request.

## The skeleton

```json
{
  "from":    0,
  "size":    10,
  "query":   { },
  "facets":  { },
  "sort":    { "fieldName": "asc" },
  "includes": ["field1", "field2"],
  "excludes": ["field3"]
}
```

## Field-by-field (plain English)

| Field | What it means | Default |
|-------|---------------|---------|
| `from` | [[Glossary#Pagination\|Pagination]] offset — skip this many results. `0` = start from the top. | `0` |
| `size` | How many results to return on this page. | `10` |
| `query` | The actual search — a JSON object describing what to match. See [[04 Query Operators]]. | — |
| `facets` | Optional summary counts (grouped by field). See [[08 Facets]]. | — |
| `sort` | Which field to order by, ascending (`"asc"`) or descending (`"desc"`). | — |
| `includes` | Allowlist — return ONLY these fields on each result. | all fields |
| `excludes` | Denylist — return everything EXCEPT these fields. | none |

## Newbie tips

- `from` and `size` together = **pagination**. Page 3 of 10-per-page = `"from": 20, "size": 10`.
- Use `includes` to keep responses small. If your UI only shows title + date, ask for just those two — networks like that.
- `excludes` is good for hiding huge fields like `documentTextContent` (the full OCR'd text of a PDF) when you don't need them.
- `includes` and `excludes` happen at the end of the [[07 Aggregation Pipeline|pipeline]], after filtering. They don't make the search faster, just the response smaller.

---

**Navigation:** [[02 Endpoints|← prev]] · [[search-logic|↑ index]] · [[04 Query Operators|next →]]
