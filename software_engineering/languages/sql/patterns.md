# SQL Patterns & Best Practices

**Focus**: Common patterns, best practices, and advanced SQL techniques for data engineering.

## Table of Contents

- [CTEs for Multi-Step Transforms](#ctes-for-multi-step-transforms)
- [Window Functions Patterns](#window-functions-patterns)
- [Join Patterns & Audit](#join-patterns--audit)
- [Data Quality Checks](#data-quality-checks)
- [Incremental Load Patterns](#incremental-load-patterns)
- [Upsert Patterns](#upsert-patterns)
- [Best Practices](#best-practices)

---

## CTEs for Multi-Step Transforms

**Use CTEs to break complex queries into readable steps.**

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
),
aggregated AS (
  SELECT
    DATE_TRUNC('day', dt) AS day,
    COUNT(*) AS rows,
    SUM(value_num) AS total
  FROM clean
  GROUP BY 1
)
SELECT *
FROM aggregated
ORDER BY 1;
```

**Benefits**: Readable, testable steps; can reference previous CTEs; easier to debug.

---

## Window Functions Patterns

### Dedupe: Keep Latest Record Per ID

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

**Use case**: Remove duplicates, keep most recent record per key.

### Top-N Per Group

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

**Use case**: Find top N records per category/group.

**Note**: `DENSE_RANK()` vs `RANK()` - `DENSE_RANK()` has no gaps (1,2,2,3), `RANK()` has gaps (1,2,2,4).

### Running Totals

```sql
SELECT
  id,
  dt,
  value,
  SUM(value) OVER (PARTITION BY id ORDER BY dt) AS running_total
FROM fact.sales;
```

**Use case**: Calculate cumulative sums, running averages, etc.

### Previous/Next Row Values

```sql
SELECT
  id,
  dt,
  value,
  LAG(value) OVER (PARTITION BY id ORDER BY dt) AS prev_value,
  LEAD(value) OVER (PARTITION BY id ORDER BY dt) AS next_value
FROM fact.sales;
```

**Use case**: Compare current row to previous/next row (e.g., calculate deltas).

---

## Join Patterns & Audit

### Basic LEFT JOIN

```sql
SELECT
  f.id,
  f.dt,
  f.value,
  d.category_name
FROM fact.sales f
LEFT JOIN dim.category d
  ON f.category_id = d.category_id;
```

### Join Audit: Track Unmatched Rows

```sql
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

**Use case**: Data quality check - identify fact rows without matching dimension records.

### Join with Multiple Conditions

```sql
SELECT
  f.*,
  d.category_name
FROM fact.sales f
LEFT JOIN dim.category d
  ON f.category_id = d.category_id
  AND f.dt >= d.effective_date
  AND f.dt < d.expiry_date;                                    -- temporal join
```

**Use case**: SCD Type 2 dimensions, time-based joins.

---

## Data Quality Checks

### Null Counts

```sql
SELECT
  COUNT(*) AS rows,
  SUM(CASE WHEN id IS NULL THEN 1 ELSE 0 END) AS null_id,
  SUM(CASE WHEN dt IS NULL THEN 1 ELSE 0 END) AS null_dt,
  SUM(CASE WHEN value IS NULL THEN 1 ELSE 0 END) AS null_value
FROM raw.events;
```

**Use case**: Quick data quality check - count nulls per column.

### Duplicate Keys

```sql
SELECT id, COUNT(*) AS cnt
FROM raw.events
GROUP BY id
HAVING COUNT(*) > 1;
```

**Use case**: Identify duplicate primary keys before loading.

### Range Violations

```sql
SELECT *
FROM raw.events
WHERE value < 0 OR value > 1000000;
```

**Use case**: Validate business rules (e.g., values must be in valid range).

### Data Type Validation

```sql
SELECT *
FROM raw.events
WHERE TRY_CAST(value AS DECIMAL(18,2)) IS NULL
  AND value IS NOT NULL;                                        -- non-null but not numeric
```

**Use case**: Identify rows that fail type conversion.

---

## Incremental Load Patterns

### Watermark Pattern

```sql
SELECT *
FROM raw.events
WHERE dt > (SELECT last_loaded_dt FROM control.watermarks WHERE pipeline_name = 'events');
```

**Use case**: Load only new/changed data since last run.

### Incremental with CTE

```sql
WITH watermark AS (
  SELECT COALESCE(MAX(last_loaded_dt), DATE '1900-01-01') AS last_dt
  FROM control.watermarks
  WHERE pipeline_name = 'events'
)
SELECT e.*
FROM raw.events e
CROSS JOIN watermark w
WHERE e.dt > w.last_dt;
```

**Use case**: More explicit watermark handling.

### Change Data Capture (CDC) Pattern

```sql
SELECT *
FROM raw.events
WHERE updated_at > (SELECT last_loaded_dt FROM control.watermarks WHERE pipeline_name = 'events')
  OR created_at > (SELECT last_loaded_dt FROM control.watermarks WHERE pipeline_name = 'events');
```

**Use case**: Track both inserts and updates.

---

## Upsert Patterns

### MERGE Pattern (Standard)

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

**Use case**: Update existing records, insert new ones.

### MERGE with Additional Conditions

```sql
MERGE INTO dim.customer AS tgt
USING stg.customer AS src
ON tgt.customer_id = src.customer_id
WHEN MATCHED AND src.updated_at > tgt.updated_at THEN
  UPDATE SET
    name = src.name,
    updated_at = src.updated_at
WHEN NOT MATCHED THEN
  INSERT (customer_id, name, created_at, updated_at)
  VALUES (src.customer_id, src.name, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP);
```

**Use case**: Only update if source is newer (idempotent loads).

### DELETE + INSERT Pattern (Alternative)

```sql
-- Delete existing
DELETE FROM dim.customer
WHERE customer_id IN (SELECT customer_id FROM stg.customer);

-- Insert all
INSERT INTO dim.customer (customer_id, name, created_at, updated_at)
SELECT customer_id, name, CURRENT_TIMESTAMP, CURRENT_TIMESTAMP
FROM stg.customer;
```

**Use case**: Full refresh pattern (when MERGE not available or simpler).

---

## Best Practices

### Use CTEs for Readability

✅ **Good**: Break complex queries into named steps
```sql
WITH base AS (SELECT ...),
     clean AS (SELECT ... FROM base WHERE ...),
     aggregated AS (SELECT ... FROM clean GROUP BY ...)
SELECT * FROM aggregated;
```

❌ **Avoid**: Deeply nested subqueries
```sql
SELECT * FROM (
  SELECT * FROM (
    SELECT * FROM ...
  ) ...
) ...
```

### Always Use WHERE Clauses

✅ **Good**: Filter early to reduce data volume
```sql
SELECT * FROM large_table WHERE dt >= DATE '2025-01-01';
```

❌ **Avoid**: Filtering after joins/aggregations when possible
```sql
SELECT * FROM large_table WHERE dt >= DATE '2025-01-01' GROUP BY ... HAVING dt >= DATE '2025-01-01';
```

### Use Parameterized Queries

✅ **Good**: Use parameters for dynamic values (prevents SQL injection)
```sql
-- In application code
SELECT * FROM events WHERE dt >= ? AND category = ?;
```

❌ **Avoid**: String concatenation for dynamic SQL
```sql
-- Dangerous
SELECT * FROM events WHERE dt >= '" + date + "' AND category = '" + cat + "';
```

### Validate Data Early

✅ **Good**: Check data quality in staging layer
```sql
CREATE TABLE stg.events_clean AS
SELECT *
FROM raw.events
WHERE id IS NOT NULL 
  AND dt IS NOT NULL
  AND value BETWEEN 0 AND 1000000;
```

### Use Appropriate Join Types

- **INNER JOIN**: When you need matching records only
- **LEFT JOIN**: When you need all left table rows (facts) regardless of match
- **Avoid RIGHT JOIN**: Use LEFT JOIN with reversed table order for clarity

### Index Considerations

- Index foreign keys for join performance
- Index date columns for time-based filters
- Index columns used in WHERE clauses
- Consider composite indexes for multi-column filters

### Test Incremental Logic

✅ **Good**: Test watermark logic with sample data
```sql
-- Test: Should return only new records
SELECT * FROM raw.events
WHERE dt > (SELECT last_loaded_dt FROM control.watermarks WHERE pipeline_name = 'events');
```

### Document Complex Queries

✅ **Good**: Add comments for business logic
```sql
-- Dedupe: Keep latest record per customer_id based on updated_at
WITH ranked AS (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY updated_at DESC) AS rn
  FROM raw.customer_updates
)
SELECT * FROM ranked WHERE rn = 1;
```

---

> **Note**: Patterns may vary by engine. Always test and validate patterns for your specific database system.

