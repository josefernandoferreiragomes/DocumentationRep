# SQL to KQL Quick Reference

This guide explains how common SQL concepts map to KQL (Kusto Query Language) syntax.

- `SELECT * FROM Table` becomes just the table name: `Table`
- `SELECT col1, col2 FROM Table` becomes: `Table | project col1, col2`
- `WHERE` clause uses `where`: `Table | where col == "value"`
- Logical operators: `AND` / `OR` become `and` / `or`
- `ORDER BY col ASC` is `| sort by col asc`, and DESC is `| sort by col desc`
- `LIMIT 10` or `TOP 10` is `| take 10`
- Aggregates like `COUNT(*)`, `AVG(col)`, `SUM(col)` use `| summarize count()`, etc.
- Grouping is done with `summarize` and `by`: `| summarize count() by col`
- `JOIN` syntax: `Table1 | join kind=inner Table2 on common_field`
- Set operators like `IN` and `NOT IN` become: `| where col in ("value1", "value2")`
- Null checks use `isnull()` and `isnotnull()`
- Pattern matching: `LIKE '%text%'` is `contains`, `LIKE 'text%'` is `startswith`, `LIKE '%text'` is `endswith`
- `BETWEEN val1 AND val2` becomes: `| where col between (val1 .. val2)`
- Subqueries use `let`: define a temporary table and reuse it
- `CASE WHEN` becomes `extend newcol = case(...)`
- `DISTINCT col1, col2` is `| distinct col1, col2`
- `UPDATE`, `DELETE` are **not supported** in KQL (read-only language)

---

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
| `LIKE '%word%'`                      | `Table \| where col has "word"`                               |
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

## Example KQL Query Using `let` With an Inline String List

```kql
// Define an inline list of valid status strings
let ValidStatuses = dynamic(["Active", "Pending", "InReview"]);

// Filter a table using that list
MyTable
| where Status in (ValidStatuses)
| project Id, Name, Status
```

**Explanation:**

- `let` defines a reusable variable (`ValidStatuses`) as a dynamic array (string list).
- The `in (...)` syntax is then used to filter records where the `Status` column is one of the items in the list.

This approach is great for cleaner and more reusable filters, especially if the list is long or reused across multiple parts of the query.

## Other advanced scenarios

A client calls gatewayUrl A
A calls gatewayUrl B
B calls the backend

We're trying to detect when A is slow (≥ 60s) while B is fast (≤ 49s)

1. You have one log table (LogTable) containing both requests.
2. Requests are correlated using a shared field — typically traceparent, or something derived like operation_Id.
3. gatewayUrl distinguishes A from B.
4. Both A and B log their own timeTaken.

```kql
// Step 1: Find all requests to gatewayUrl A that took > 59s
let SlowA = LogTable
| where gatewayUrl contains "A"
  and timeTaken > 59s
| project A_traceparent = traceparent, A_timeTaken = timeTaken, A_gatewayUrl = gatewayUrl;

// Step 2: Find requests to gatewayUrl B that took < 50s
let FastB = LogTable
| where gatewayUrl contains "B"
  and timeTaken < 50s
| project traceparent, B_timeTaken = timeTaken, B_gatewayUrl = gatewayUrl;

// Step 3: Join A with B on traceparent
SlowA
| join kind=inner (
    FastB
) on $left.A_traceparent == $right.traceparent
| project A_gatewayUrl, A_timeTaken, B_gatewayUrl, B_timeTaken, A_traceparent
| sort by A_timeTaken desc
```
If a column has the same name in both tables, it is sufficed with the next integer, according to join position. 
Example for userName:
(...) 
| project username, userName1

## References
https://learn.microsoft.com/en-us/kusto/query/kql-quick-reference?view=microsoft-fabric
