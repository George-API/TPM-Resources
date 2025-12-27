# SQL for Modern Data Engineering

## Table of Contents

- [Core Syntax](#core-syntax)
- [Common Usage & Patterns](#common-usage--patterns)
- [SELECT, FROM, WHERE (filtering)](#select-from-where-filtering)
- [Aliases, Expressions, CASE, NULL handling](#aliases-expressions-case-null-handling)
- [ORDER BY, LIMIT / FETCH (top-N inspection)](#order-by-limit--fetch-top-n-inspection)
- [Aggregations: GROUP BY + HAVING](#aggregations-group-by--having)
- [Date / Time: EXTRACT, DATE_TRUNC (engine-specific), windows](#date--time-extract-date_trunc-engine-specific-windows)
- [Joins: INNER / LEFT and join "audit"](#joins-inner--left-and-join-audit)
- [UNION / UNION ALL (stack datasets)](#union--union-all-stack-datasets)
- [CTEs (WITH) for readable multi-step transforms](#ctes-with-for-readable-multi-step-transforms)
- [Window Functions (dedupe, top-N, running totals)](#window-functions-dedupe-top-n-running-totals)
- [Data Quality Checks (fast, practical)](#data-quality-checks-fast-practical)
- [DDL Basics (tables/views) + CTAS staging](#ddl-basics-tablesviews--ctas-staging)
- [DML Basics (INSERT/UPDATE/DELETE) + "upsert" patterns](#dml-basics-insertupdatedelete--upsert-patterns)
- [Transactions (when supported/needed)](#transactions-when-supportedneeded)

---

## Core Syntax

### Query Structure
- `SELECT col1, col2` - choose columns
- `FROM table` - choose source table/view
- `WHERE condition` - filter rows (pre-agg)
- `ORDER BY col ASC/DESC` - sort ascending/descending
- `LIMIT n` - return top n (engine-specific)
- `FETCH FIRST n ROWS ONLY` - return top n (ANSI-ish)
- `GROUP BY col1, col2` - aggregate grouping
- `HAVING condition` - filter after GROUP BY

### Logical Operators
- `AND` - combine conditions (all true)
- `OR` - combine conditions (any true)
- `NOT` - negate condition

### Filtering
- `IN (v1, v2, ...)` - membership filter
- `BETWEEN a AND b` - inclusive range filter
- `LIKE '%pattern%'` - string pattern match
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
- `JOIN` - join tables
- `INNER JOIN` - matching rows only
- `LEFT JOIN` - keep left table rows
- `UNION` - combine + de-dup
- `UNION ALL` - combine + keep duplicates

### Window Functions
- `ROW_NUMBER() OVER (PARTITION BY k ORDER BY dt DESC)` - dedupe/latest
- `DENSE_RANK() OVER (PARTITION BY k ORDER BY v DESC)` - top-N per group
- `LAG(v) OVER (PARTITION BY k ORDER BY dt)` - previous row value

### Common Table Expressions
- `WITH cte AS ( ... )` - named subquery (CTE)

### Transactions
- `BEGIN` - start transaction
- `COMMIT` - commit transaction
- `ROLLBACK` - rollback transaction

### DDL (Data Definition Language)
- `CREATE TABLE t ( ... )` - create table
- `CREATE VIEW v AS SELECT ...` - create view

### DML (Data Manipulation Language)
- `INSERT INTO t (...) SELECT ...` - insert from query
- `UPDATE t SET col = expr WHERE ...` - update rows
- `DELETE FROM t WHERE ...` - delete rows
- `MERGE INTO tgt USING src ON ...` - upsert/merge (engine-specific)

### Query Analysis
- `EXPLAIN SELECT ...` - view query plan (engine-specific)


## Common Usage & Patterns

**Note**: ANSI-ish SQL; some functions vary by engine (Postgres/SQL Server/BigQuery/Snowflake).

## SELECT, FROM, WHERE (filtering)

```sql
SELECT id, dt, value
FROM raw.events
WHERE dt >= DATE '2025-01-01' AND value IS NOT NULL;          -- basic filter + null check

SELECT *
FROM raw.events
WHERE category IN ('A','B') AND value BETWEEN 0 AND 100;      -- IN + BETWEEN

SELECT id, value
FROM raw.events
WHERE category LIKE '%test%' OR category LIKE '%demo%';       -- pattern match (basic)
```


## Aliases, Expressions, CASE, NULL handling

```sql
SELECT
  e.id AS event_id,                                           -- AS alias
  COALESCE(NULLIF(TRIM(e.category), ''), 'unknown') AS cat,    -- normalize blank -> null -> default
  CASE WHEN e.value >= 100 THEN 'gold'                         -- CASE for bucketing
       WHEN e.value >= 50  THEN 'silver'
       ELSE 'bronze'
  END AS tier
FROM raw.events e;
```


## ORDER BY, LIMIT / FETCH (top-N inspection)

```sql
SELECT id, dt, value
FROM raw.events
ORDER BY dt DESC
LIMIT 100;                                                    -- Postgres/MySQL/Snowflake (use FETCH FIRST on some DBs)

SELECT id, dt, value
FROM raw.events
ORDER BY dt DESC
FETCH FIRST 100 ROWS ONLY;                                    -- ANSI-ish alternative
```


## Aggregations: GROUP BY + HAVING

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
HAVING SUM(value) > 0                                         -- post-aggregation filter
ORDER BY total_value DESC;
```


## Date / Time: EXTRACT, DATE_TRUNC (engine-specific), windows

```sql
SELECT
  DATE_TRUNC('day', dt) AS day,                               -- adjust per engine
  EXTRACT(YEAR FROM dt) AS yr,
  COUNT(*) AS rows
FROM fact.sales
GROUP BY 1, 2
ORDER BY 1, 2;

-- Incremental filter (watermark)
SELECT *
FROM raw.events
WHERE dt > (SELECT last_loaded_dt FROM control.watermarks WHERE pipeline_name = 'events');
```


## Joins: INNER / LEFT and join "audit"

```sql
SELECT
  f.id,
  f.dt,
  f.value,
  d.category_name
FROM fact.sales f
LEFT JOIN dim.category d
  ON f.category_id = d.category_id;                           -- keep all facts, enrich if dim exists

-- Join audit via indicator-style (portable approach)
SELECT
  CASE
    WHEN d.category_id IS NULL THEN 'unmatched_dim'
    ELSE 'matched'
  END AS join_status,
  COUNT(*) AS rows
FROM fact.sales f
LEFT JOIN dim.category d
  ON f.category_id = d.category_id
GROUP BY 1;
```


## UNION / UNION ALL (stack datasets)

```sql
SELECT id, dt, value FROM raw.events_2024
UNION ALL                                                     -- keep duplicates; typical for staging unions
SELECT id, dt, value FROM raw.events_2025;
```


## CTEs (WITH) for readable multi-step transforms

```sql
WITH base AS (
  SELECT
    id,
    dt,
    TRY_CAST(value AS DECIMAL(18,2)) AS value_num            -- TRY_CAST is engine-specific
  FROM raw.events
),
clean AS (
  SELECT *
  FROM base
  WHERE value_num IS NOT NULL
)
SELECT
  DATE_TRUNC('day', dt) AS day,
  COUNT(*) AS rows,
  SUM(value_num) AS total
FROM clean
GROUP BY 1
ORDER BY 1;
```


## Window Functions (dedupe, top-N, running totals)

### Dedupe: keep latest record per id

```sql
WITH ranked AS (
  SELECT
    e.*,
    ROW_NUMBER() OVER (PARTITION BY id ORDER BY dt DESC) AS rn
  FROM raw.events e
)
SELECT *
FROM ranked
WHERE rn = 1;
```

### Top-N per group

```sql
WITH r AS (
  SELECT
    category,
    id,
    value,
    DENSE_RANK() OVER (PARTITION BY category ORDER BY value DESC) AS rnk
  FROM fact.sales
)
SELECT *
FROM r
WHERE rnk <= 3;
```

### Running sum (time ordered)

```sql
SELECT
  id,
  dt,
  value,
  SUM(value) OVER (PARTITION BY id ORDER BY dt) AS running_total
FROM fact.sales;
```


## Data Quality Checks (fast, practical)

### Null counts

```sql
SELECT
  COUNT(*) AS rows,
  SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END) AS null_id,
  SUM(CASE WHEN dt IS NULL THEN 1 ELSE 0 END) AS null_dt
FROM raw.events;
```

### Duplicate keys

```sql
SELECT id, COUNT(*) AS cnt
FROM raw.events
GROUP BY id
HAVING COUNT(*) > 1;
```

### Range violations

```sql
SELECT *
FROM raw.events
WHERE value < 0 OR value > 1000000;
```


## DDL Basics (tables/views) + CTAS staging

```sql
CREATE TABLE stg.events_clean AS
SELECT
  id,
  dt,
  value,
  COALESCE(NULLIF(TRIM(category), ''), 'unknown') AS category
FROM raw.events
WHERE id IS NOT NULL AND dt IS NOT NULL;

CREATE VIEW mart.daily_sales AS
SELECT
  DATE_TRUNC('day', dt) AS day,
  category_id,
  SUM(value) AS total_value
FROM fact.sales
GROUP BY 1, 2;
```


## DML Basics (INSERT/UPDATE/DELETE) + "upsert" patterns

### INSERT

```sql
INSERT INTO stg.events_clean (id, dt, value, category)
SELECT id, dt, value, category
FROM raw.events
WHERE dt >= DATE '2025-12-01';
```

### UPDATE

```sql
UPDATE dim.category
SET category_name = TRIM(category_name)
WHERE category_name LIKE ' %' OR category_name LIKE '% ';     -- trim-ish cleanup
```

### DELETE

```sql
DELETE FROM raw.events
WHERE dt < DATE '2020-01-01';                                 -- remove old bad history (use carefully)
```

### Upsert / Merge (engine-specific template)

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


## Transactions (when supported/needed)

```sql
BEGIN;
-- ... multiple statements ...
COMMIT;                                                        -- or ROLLBACK;
```
