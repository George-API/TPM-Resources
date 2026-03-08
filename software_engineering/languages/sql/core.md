# SQL Core Syntax Reference

**Focus**: Core SQL syntax, operation flow/order, and basic query structure.

## Table of Contents

- [SQL Operation Flow/Order](#sql-operation-floworder)
- [Core Syntax](#core-syntax)
- [Basic Query Examples](#basic-query-examples)
- [DDL Basics](#ddl-basics)
- [DML Basics](#dml-basics)
- [Transactions](#transactions)

---

## SQL Operation Flow/Order

**Query execution order** (logical, not necessarily physical):

1. `FROM` - identify source tables/views
2. `JOIN` - join tables
3. `WHERE` - filter rows (pre-aggregation)
4. `GROUP BY` - group rows for aggregation
5. `HAVING` - filter groups (post-aggregation)
6. `SELECT` - select columns/expressions
7. `ORDER BY` - sort results
8. `LIMIT` / `FETCH` - limit result set

**Note**: This is logical order; optimizers may reorder operations.

---

## Core Syntax

### Query Structure

- `SELECT col1, col2` - choose columns
- `FROM table` - choose source table/view
- `WHERE condition` - filter rows (pre-agg)
- `GROUP BY col1, col2` - aggregate grouping
- `HAVING condition` - filter after GROUP BY
- `ORDER BY col ASC/DESC` - sort ascending/descending
- `LIMIT n` - return top n (engine-specific)
- `FETCH FIRST n ROWS ONLY` - return top n (ANSI-ish)

### Logical Operators

- `AND` - combine conditions (all true)
- `OR` - combine conditions (any true)
- `NOT` - negate condition

### Filtering

- `IN (v1, v2, ...)` - membership filter
- `BETWEEN a AND b` - inclusive range filter
- `LIKE '%pattern%'` - string pattern match (`%` = any chars, `_` = single char)
- `IS NULL` - null check
- `IS NOT NULL` - not-null check

### Aliases & Expressions

- `AS` - alias column/table
- `DISTINCT` - unique rows/values
- `CASE WHEN cond THEN x ELSE y END` - conditional expression
- `COALESCE(a, b, c)` - first non-null
- `NULLIF(a, b)` - null if equal
- `CAST(x AS TYPE)` - type cast

### Aggregations

- `COUNT(*)` - row count
- `COUNT(DISTINCT col)` - unique count
- `SUM(col)` - sum aggregation
- `AVG(col)` - average aggregation
- `MIN(col)` - min aggregation
- `MAX(col)` - max aggregation

### Joins & Combining

- `INNER JOIN` - matching rows only
- `LEFT JOIN` - keep left table rows
- `RIGHT JOIN` - keep right table rows
- `FULL OUTER JOIN` - keep all rows from both tables
- `CROSS JOIN` - cartesian product
- `UNION` - combine + de-dup
- `UNION ALL` - combine + keep duplicates

### Window Functions

- `ROW_NUMBER() OVER (PARTITION BY k ORDER BY dt DESC)` - dedupe/latest
- `DENSE_RANK() OVER (PARTITION BY k ORDER BY v DESC)` - top-N per group
- `RANK() OVER (PARTITION BY k ORDER BY v DESC)` - rank with gaps
- `LAG(v) OVER (PARTITION BY k ORDER BY dt)` - previous row value
- `LEAD(v) OVER (PARTITION BY k ORDER BY dt)` - next row value
- `SUM(v) OVER (PARTITION BY k ORDER BY dt)` - running sum

### Common Table Expressions

- `WITH cte AS ( ... )` - named subquery (CTE)

### Date/Time Functions

- `EXTRACT(YEAR FROM dt)` - extract date part
- `DATE_TRUNC('day', dt)` - truncate to unit (engine-specific)
- `CURRENT_DATE` - current date
- `CURRENT_TIMESTAMP` - current timestamp

### Transactions

- `BEGIN` - start transaction
- `COMMIT` - commit transaction
- `ROLLBACK` - rollback transaction

### DDL (Data Definition Language)

- `CREATE TABLE t (col1 TYPE, col2 TYPE)` - create table
- `CREATE VIEW v AS SELECT ...` - create view
- `CREATE TABLE t AS SELECT ...` - create table as select (CTAS)
- `ALTER TABLE t ADD COLUMN col TYPE` - add column
- `DROP TABLE t` - drop table
- `DROP VIEW v` - drop view

### DML (Data Manipulation Language)

- `INSERT INTO t (col1, col2) VALUES (v1, v2)` - insert values
- `INSERT INTO t (...) SELECT ...` - insert from query
- `UPDATE t SET col = expr WHERE ...` - update rows
- `DELETE FROM t WHERE ...` - delete rows
- `MERGE INTO tgt USING src ON ...` - upsert/merge (engine-specific)

### Query Analysis

- `EXPLAIN SELECT ...` - view query plan (engine-specific)

---

## Basic Query Examples

### SELECT, FROM, WHERE

```sql
SELECT id, dt, value
FROM raw.events
WHERE dt >= DATE '2025-01-01' AND value IS NOT NULL;

SELECT *
FROM raw.events
WHERE category IN ('A','B') AND value BETWEEN 0 AND 100;

SELECT id, value
FROM raw.events
WHERE category LIKE '%test%' OR category LIKE '%demo%';
```

### Aliases, Expressions, CASE, NULL handling

```sql
SELECT
  e.id AS event_id,
  COALESCE(NULLIF(TRIM(e.category), ''), 'unknown') AS cat,
  CASE WHEN e.value >= 100 THEN 'gold'
       WHEN e.value >= 50  THEN 'silver'
       ELSE 'bronze'
  END AS tier
FROM raw.events e;
```

### ORDER BY, LIMIT / FETCH

```sql
SELECT id, dt, value
FROM raw.events
ORDER BY dt DESC
LIMIT 100;                                    -- Postgres/MySQL/Snowflake

SELECT id, dt, value
FROM raw.events
ORDER BY dt DESC
FETCH FIRST 100 ROWS ONLY;                   -- ANSI-ish alternative
```

### Aggregations: GROUP BY + HAVING

```sql
SELECT
  category,
  COUNT(*)              AS rows,
  COUNT(DISTINCT id)    AS unique_ids,
  SUM(value)            AS total_value,
  AVG(value)            AS avg_value
FROM fact.sales
WHERE dt >= DATE '2025-01-01'
GROUP BY category
HAVING SUM(value) > 0                         -- post-aggregation filter
ORDER BY total_value DESC;
```

### Joins

```sql
SELECT
  f.id,
  f.dt,
  f.value,
  d.category_name
FROM fact.sales f
INNER JOIN dim.category d
  ON f.category_id = d.category_id;

SELECT
  f.id,
  f.dt,
  f.value,
  d.category_name
FROM fact.sales f
LEFT JOIN dim.category d
  ON f.category_id = d.category_id;          -- keep all facts, enrich if dim exists
```

### UNION / UNION ALL

```sql
SELECT id, dt, value FROM raw.events_2024
UNION ALL                                     -- keep duplicates
SELECT id, dt, value FROM raw.events_2025;
```

### Date / Time

```sql
SELECT
  DATE_TRUNC('day', dt) AS day,               -- adjust per engine
  EXTRACT(YEAR FROM dt) AS yr,
  COUNT(*) AS rows
FROM fact.sales
GROUP BY 1, 2
ORDER BY 1, 2;
```

---

## DDL Basics

### CREATE TABLE

```sql
CREATE TABLE stg.events_clean (
  id VARCHAR(50),
  dt DATE,
  value DECIMAL(18,2),
  category VARCHAR(100)
);
```

### CREATE TABLE AS SELECT (CTAS)

```sql
CREATE TABLE stg.events_clean AS
SELECT
  id,
  dt,
  value,
  COALESCE(NULLIF(TRIM(category), ''), 'unknown') AS category
FROM raw.events
WHERE id IS NOT NULL AND dt IS NOT NULL;
```

### CREATE VIEW

```sql
CREATE VIEW mart.daily_sales AS
SELECT
  DATE_TRUNC('day', dt) AS day,
  category_id,
  SUM(value) AS total_value
FROM fact.sales
GROUP BY 1, 2;
```

---

## DML Basics

### INSERT

```sql
INSERT INTO stg.events_clean (id, dt, value, category)
VALUES ('123', DATE '2025-01-01', 100.00, 'A');

INSERT INTO stg.events_clean (id, dt, value, category)
SELECT id, dt, value, category
FROM raw.events
WHERE dt >= DATE '2025-12-01';
```

### UPDATE

```sql
UPDATE dim.category
SET category_name = TRIM(category_name)
WHERE category_name LIKE ' %' OR category_name LIKE '% ';
```

### DELETE

```sql
DELETE FROM raw.events
WHERE dt < DATE '2020-01-01';
```

### MERGE (Upsert)

```sql
MERGE INTO dim.customer AS tgt
USING stg.customer AS src
ON tgt.customer_id = src.customer_id
WHEN MATCHED THEN
  UPDATE SET
    name = src.name,
    updated_at = CURRENT_TIMESTAMP
WHEN NOT MATCHED THEN
  INSERT (customer_id, name, created_at, updated_at)
  VALUES (src.customer_id, src.name, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);
```

---

## Transactions

```sql
BEGIN;
-- ... multiple statements ...
COMMIT;                                       -- or ROLLBACK;
```

**Note**: Transaction support varies by engine. Some engines auto-commit each statement.

---

> **Note**: ANSI-ish SQL; some functions vary by engine (Postgres/SQL Server/BigQuery/Snowflake). Always check engine-specific documentation for exact syntax.

