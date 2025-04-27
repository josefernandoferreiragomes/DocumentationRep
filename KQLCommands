# SQL to KQL Quick Reference (Markdown Version)

|   |
| - |

| **SQL**                              | **KQL**                                                            |
| ------------------------------------ | ------------------------------------------------------------------ |
| `SELECT * FROM Table`                | `Table`                                                            |
| `SELECT col1, col2 FROM Table`       | `Table \| project col1, col2`                                      |
| `WHERE col = 'value'`                | `Table \| where col == "value"`                                    |
| `AND`, `OR`                          | `and`, `or`                                                        |
| `ORDER BY col ASC`                   | `Table \| sort by col asc`                                         |
| `ORDER BY col DESC`                  | `Table \| sort by col desc`                                        |
| `LIMIT 10` / `TOP 10`                | `Table \| take 10`                                                 |
| `COUNT(*)`                           | `Table \| summarize count()`                                       |
| `GROUP BY col`                       | `Table \| summarize count() by col`                                |
| `AVG(col)`                           | `Table \| summarize avg(col)`                                      |
| `SUM(col)`                           | `Table \| summarize sum(col)`                                      |
| `MIN(col)`                           | `Table \| summarize min(col)`                                      |
| `MAX(col)`                           | `Table \| summarize max(col)`                                      |
| `JOIN`                               | `Table1 \| join kind=inner Table2 on common_field`                 |
| `IN ('value1', 'value2')`            | `Table \| where col in ("value1", "value2")`                       |
| `NOT IN ('value1', 'value2')`        | `Table \| where col !in ("value1", "value2")`                      |
| `IS NULL`                            | `Table \| where isnull(col)`                                       |
| `IS NOT NULL`                        | `Table \| where isnotnull(col)`                                    |
| `LIKE '%text%'`                      | `Table \| where col contains "text"`                               |
| `LIKE 'text%'`                       | `Table \| where col startswith "text"`                             |
| `LIKE '%text'`                       | `Table \| where col endswith "text"`                               |
| `BETWEEN val1 AND val2`              | `Table \| where col between (val1 .. val2)`                        |
| `Subquery (SELECT FROM (SELECT...))` | `let TempTable = (query) \n TempTable \| next_query`               |
| `CASE WHEN`                          | `Table \| extend newcol = case(condition1, result1, ..., default)` |
| `DISTINCT col1, col2`                | `Table \| distinct col1, col2`                                     |
| `UPDATE`, `DELETE`                   | **Not Supported (read-only)**                                      |

---

**Notes:**

- In real KQL scripts, just use `|` directly. No need to escape it.
- KQL is designed for querying only, not for data modification.
- `let` is used in KQL to create subqueries or temporary tables.

**Example Real KQL Usage:**

```kql
Table
| where col == "value"
| project col1, col2
| sort by col asc
| take 10
```

**Example Using **``** in KQL:**

```kql
let TempTable = Table
| where status == "Active"
| project id, name;

TempTable
| sort by name asc
| take 5
```

