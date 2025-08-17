
# SQL Server vs PostgreSQL Cheat Sheet

This document provides a quick reference comparison between **SQL Server** and **PostgreSQL** commands.

---

## 🔎 Selecting Rows

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| Select top N rows | `SELECT TOP 10 * FROM Users;` | `SELECT * FROM Users LIMIT 10;` |
| Pagination | `OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;` | `LIMIT 10 OFFSET 20;` |

---

## 📋 String Operations

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| Concatenate | `'Hello ' + 'World'` <br> or `CONCAT('Hello ', 'World')` | `'Hello ' || 'World'` <br> or `CONCAT('Hello ', 'World')` |
| String length | `LEN(Name)` | `LENGTH(Name)` |
| Substring | `SUBSTRING(Name, 1, 3)` | `SUBSTRING(Name FROM 1 FOR 3)` |
| Lowercase | `LOWER(Name)` | `LOWER(Name)` |
| Uppercase | `UPPER(Name)` | `UPPER(Name)` |

---

## 📅 Date & Time

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| Current timestamp | `GETDATE()` | `NOW()` |
| Current UTC timestamp | `GETUTCDATE()` | `CURRENT_TIMESTAMP AT TIME ZONE 'UTC'` |
| Add days | `DATEADD(DAY, 7, GETDATE())` | `NOW() + INTERVAL '7 days'` |
| Date difference | `DATEDIFF(DAY, StartDate, EndDate)` | `AGE(EndDate, StartDate)` or `EXTRACT(DAY FROM EndDate - StartDate)` |

---

## 🔢 Numbers & Aggregates

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| Round | `ROUND(Value, 2)` | `ROUND(Value, 2)` |
| Random number | `RAND()` | `RANDOM()` |
| Integer division | `10 / 3 → 3` | `10 / 3 → 3.333…` (use `FLOOR()` for integer) |

---

## 🧩 DDL (Table Creation)

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| Auto-increment ID | `Id INT IDENTITY(1,1)` | `Id SERIAL` or `Id INT GENERATED ALWAYS AS IDENTITY` |
| Text column | `NVARCHAR(100)` | `VARCHAR(100)` or `TEXT` |
| Boolean | `BIT` | `BOOLEAN` |

---

## 🔍 Conditions & Logic

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| IF in query | `CASE WHEN Age > 18 THEN 'Adult' ELSE 'Minor' END` | `CASE WHEN Age > 18 THEN 'Adult' ELSE 'Minor' END` |
| NULL check | `ISNULL(Name, 'Unknown')` | `COALESCE(Name, 'Unknown')` |

---

## 🔄 Upsert (Insert or Update)

**SQL Server**
```sql
MERGE INTO Users AS target
USING (SELECT 1 AS Id, 'John' AS Name) AS source
ON target.Id = source.Id
WHEN MATCHED THEN
  UPDATE SET Name = source.Name
WHEN NOT MATCHED THEN
  INSERT (Id, Name) VALUES (source.Id, source.Name);
```

**PostgreSQL**
```sql
INSERT INTO Users (Id, Name)
VALUES (1, 'John')
ON CONFLICT (Id) DO UPDATE
SET Name = EXCLUDED.Name;
```

---

## 📜 System Information

| Task | SQL Server | PostgreSQL |
|------|------------|-------------|
| List tables | `SELECT * FROM sys.tables;` | `SELECT * FROM pg_catalog.pg_tables;` |
| List columns | `SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'Users';` | `SELECT * FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME = 'users';` |

---

## 🧮 Stored Procedures & Functions

**SQL Server**
```sql
CREATE PROCEDURE GetUsers
AS
BEGIN
  SELECT * FROM Users;
END;
```

**PostgreSQL**
```sql
CREATE FUNCTION GetUsers()
RETURNS TABLE(Id INT, Name TEXT)
AS $$
BEGIN
  RETURN QUERY SELECT * FROM Users;
END;
$$ LANGUAGE plpgsql;
```

---

# 📚 Official References

- SQL Server Documentation (Microsoft): [https://learn.microsoft.com/en-us/sql/t-sql/language-reference](https://learn.microsoft.com/en-us/sql/t-sql/language-reference)
- PostgreSQL Documentation: [https://www.postgresql.org/docs/current/sql-commands.html](https://www.postgresql.org/docs/current/sql-commands.html)
- INFORMATION_SCHEMA Standard: [https://dev.mysql.com/doc/refman/8.0/en/information-schema.html](https://dev.mysql.com/doc/refman/8.0/en/information-schema.html)

---

✅ This cheat sheet covers the most common differences between SQL Server and PostgreSQL.
