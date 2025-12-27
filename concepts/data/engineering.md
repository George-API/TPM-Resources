# Data Engineering Patterns — Platform-Agnostic

**Scope**: Platform-agnostic patterns for building data pipelines, ETL/ELT, orchestration, and data transformation.

**Purpose**: Use this when designing and building data pipelines. For governance and quality practices, see Data Management. For analytics/BI layer patterns, see Analytics Engineering.

## Table of Contents

- [1. Pipeline Patterns](#1-pipeline-patterns)
- [2. ETL vs ELT Strategies](#2-etl-vs-elt-strategies)
- [3. Orchestration Patterns](#3-orchestration-patterns)
- [4. Transformation Patterns](#4-transformation-patterns)
- [5. Data Modeling Patterns](#5-data-modeling-patterns)
- [6. Incremental Processing](#6-incremental-processing)
- [7. Error Handling & Resilience](#7-error-handling--resilience)
- [8. Performance Optimization](#8-performance-optimization)

---

## 1. Pipeline Patterns

### Batch Processing

- **Scheduled batch**: Fixed schedule (hourly, daily, weekly)
- **Event-driven batch**: Triggered by events (file arrival, API webhook)
- **On-demand batch**: Manual or API-triggered runs
- **Best practices**:
  - Idempotent runs (safe to rerun)
  - Watermarking for incremental loads
  - Partitioning by date/tenant/source for parallelization

### Streaming Processing

- **Real-time streaming**: Continuous processing (Kafka, Event Hubs, Kinesis)
- **Micro-batch**: Small batches at high frequency (every few seconds/minutes)
- **Best practices**:
  - Handle late-arriving data (watermarks, allowed lateness)
  - Exactly-once semantics (idempotent processing)
  - Backpressure handling (throttle producers when consumers lag)

### Change Data Capture (CDC)

- **Log-based CDC**: Read database transaction logs (Debezium, AWS DMS)
- **Trigger-based CDC**: Database triggers capture changes
- **Query-based CDC**: Poll for changes using timestamps/version columns
- **Best practices**:
  - Capture deletes (soft deletes or tombstone records)
  - Handle schema changes gracefully
  - Replay capability for backfills

### Lambda Architecture

- **Batch layer**: Historical data, complete recomputation
- **Speed layer**: Real-time processing, recent data
- **Serving layer**: Merge batch + speed results
- **Use case**: When you need both historical accuracy and real-time updates

---

## 2. ETL vs ELT Strategies

### ETL (Extract, Transform, Load)

- **Pattern**: Extract → Transform (in processing engine) → Load (to destination)
- **Pros**: 
  - Transform before storage (saves storage costs)
  - Data quality enforced before landing
  - Smaller target storage footprint
- **Cons**:
  - Requires processing capacity upfront
  - Less flexible (harder to reprocess with new logic)
  - Slower initial load

### ELT (Extract, Load, Transform)

- **Pattern**: Extract → Load (raw to storage) → Transform (in destination)
- **Pros**:
  - Faster initial load (raw data lands quickly)
  - Flexible (reprocess with new logic anytime)
  - Leverage destination compute (SQL, Spark)
  - Preserve raw data for audit/replay
- **Cons**:
  - Larger storage footprint (raw + transformed)
  - Transform costs in destination system
  - Requires governance for raw data access

### Hybrid Approach

- **Bronze (Raw)**: ELT pattern (load raw, minimal validation)
- **Silver (Cleaned)**: ETL or ELT (transform in pipeline or destination)
- **Gold (Curated)**: ELT pattern (transform in destination, leverage SQL/Spark)

### Decision Factors

- **Storage cost vs compute cost**: ELT if storage is cheap, ETL if compute is expensive
- **Reprocessing needs**: ELT if you need flexibility to reprocess
- **Data quality requirements**: ETL if you need strict quality gates before landing
- **Destination capabilities**: ELT if destination has strong SQL/Spark capabilities

---

## 3. Orchestration Patterns

### DAG-Based Orchestration

- **Directed Acyclic Graph (DAG)**: Tasks with dependencies
- **Patterns**:
  - Linear: Task A → Task B → Task C
  - Parallel: Task A → [Task B, Task C] → Task D
  - Conditional: Task A → (if condition) Task B else Task C
- **Tools**: Airflow, Prefect, Dagster, Azure Data Factory

### Workflow Patterns

- **Sequential**: Run tasks one after another
- **Parallel**: Run independent tasks simultaneously
- **Fan-out/Fan-in**: Split work across multiple workers, aggregate results
- **Retry logic**: Exponential backoff, max retries, retry on specific errors

### Dependency Management

- **Upstream dependencies**: Wait for upstream tasks to complete
- **Data dependencies**: Wait for data availability (file existence, partition ready)
- **Time dependencies**: Schedule based on time (daily at 2 AM)
- **External dependencies**: Wait for external systems (API, database)

### Error Handling in Orchestration

- **Task-level retries**: Retry individual tasks
- **Pipeline-level rollback**: Rollback entire pipeline on critical failure
- **Partial success handling**: Continue pipeline if non-critical tasks fail
- **Alerting**: Notify on failures, SLA breaches, data quality issues

---

## 4. Transformation Patterns

### Data Cleansing

- **Null handling**: Fill nulls, drop nulls, impute values
- **Type conversion**: String to date, string to number (with validation)
- **Format standardization**: Phone numbers, addresses, dates
- **Encoding fixes**: UTF-8 normalization, character set conversion

### Standardization

- **Naming conventions**: Column names, table names, consistent casing
- **Value standardization**: Enums, codes, reference data mapping
- **Unit conversion**: Currency, measurements, time zones
- **Case normalization**: Uppercase, lowercase, title case

### Deduplication

- **Exact duplicates**: Hash-based detection, remove identical rows
- **Fuzzy duplicates**: Similarity matching (names, addresses)
- **Survivorship rules**: Latest timestamp, highest priority source, non-null preference
- **Deterministic keys**: Hash-based keys for stable deduplication across runs

### Enrichment

- **Lookup joins**: Enrich with reference data (dimensions, master data)
- **External APIs**: Geocoding, data validation, enrichment services
- **Calculated fields**: Derived columns, aggregations, business logic
- **Data quality flags**: Mark records with quality issues for downstream handling

### Validation

- **Schema validation**: Column types, required fields, constraints
- **Business rules**: Range checks, format validation, cross-field rules
- **Referential integrity**: Foreign key validation, orphan detection
- **Statistical validation**: Outlier detection, distribution checks

---

## 5. Data Modeling Patterns

### Normalized Models

- **Third Normal Form (3NF)**: Eliminate redundancy, separate entities
- **Use case**: Transactional systems, source systems
- **Pros**: No redundancy, easier updates, referential integrity
- **Cons**: More joins, slower queries for analytics

### Denormalized Models

- **Star Schema**: Fact tables + dimension tables
- **Wide Tables**: Flattened, all columns in one table
- **Use case**: Analytics, BI, reporting
- **Pros**: Faster queries, fewer joins, simpler for analysts
- **Cons**: Data redundancy, larger storage, harder to maintain

### Medallion Architecture

- **Bronze (Raw)**: Source-aligned, append-only, immutable
- **Silver (Cleaned)**: Standardized, deduplicated, schema-enforced
- **Gold (Curated)**: Business entities, aggregates, BI-ready
- **Pattern**: Progressive refinement, each layer serves different purposes

### Data Vault

- **Hubs**: Business keys
- **Links**: Relationships between hubs
- **Satellites**: Descriptive attributes, time-variant
- **Use case**: Enterprise data warehousing, auditability, historical tracking

### One Big Table (OBT)

- **Single wide table**: All attributes in one table
- **Use case**: Simple analytics, small datasets, ad-hoc analysis
- **Pros**: Simple, fast for small data
- **Cons**: Doesn't scale, hard to maintain, redundant data

---

## 6. Incremental Processing

### Watermarking

- **High-water mark**: Track last processed record (timestamp, ID)
- **Pattern**: Load records where `timestamp > last_watermark`
- **Storage**: Control table or metadata store
- **Best practices**: Handle timezone issues, clock skew, late arrivals

### Change Detection

- **Timestamp columns**: `updated_at`, `created_at`
- **Version columns**: Incrementing version numbers
- **CDC**: Log-based change capture
- **Hash comparison**: Compare hash of records to detect changes

### Incremental Merge

- **Upsert pattern**: Insert new records, update changed records
- **Merge strategy**: 
  - Insert-only (append new records)
  - Upsert (insert or update)
  - SCD Type 2 (insert new version, mark old as historical)
- **Idempotency**: Safe to rerun (deterministic keys, merge logic)

### Partition-Based Incremental

- **Date partitioning**: Process only new partitions (daily, hourly)
- **Pattern**: `WHERE partition_date = '2024-01-15'`
- **Benefits**: Parallel processing, faster reruns, cost optimization
- **Best practices**: Handle late-arriving data, backfill strategy

---

## 7. Error Handling & Resilience

### Retry Patterns

- **Exponential backoff**: Increase delay between retries (1s, 2s, 4s, 8s)
- **Jitter**: Add randomness to prevent thundering herd
- **Max retries**: Limit retry attempts, fail after threshold
- **Retry on specific errors**: Only retry transient errors (network, timeout), not permanent (auth, validation)

### Dead Letter Queues (DLQ)

- **Failed records**: Route to DLQ for manual review
- **Pattern**: After max retries, send to DLQ
- **DLQ processing**: Manual review, fix, replay
- **Best practices**: Classify errors (transient vs permanent), automate replay where possible

### Circuit Breaker

- **Pattern**: Stop calling failing service after threshold failures
- **States**: Closed (normal), Open (failing), Half-Open (testing)
- **Use case**: Protect downstream systems from cascading failures
- **Recovery**: Automatically retry after cooldown period

### Idempotency

- **Idempotent operations**: Safe to run multiple times (same result)
- **Patterns**:
  - Deterministic keys (hash-based)
  - Upsert logic (insert or update)
  - Idempotency keys (track processed records)
- **Best practices**: Always design for idempotency, handle duplicates gracefully

### Checkpointing

- **Save progress**: Checkpoint after processing batches
- **Recovery**: Resume from last checkpoint on failure
- **Pattern**: Process batch → checkpoint → process next batch
- **Use case**: Long-running pipelines, streaming processing

---

## 8. Performance Optimization

### Partitioning

- **Date partitioning**: Partition by date (daily, monthly)
- **Hash partitioning**: Distribute data evenly across partitions
- **Range partitioning**: Partition by value ranges
- **Best practices**: 
  - Partition by common filter columns
  - Avoid over-partitioning (too many small partitions)
  - Coalesce small partitions regularly

### Indexing

- **Primary keys**: Unique identifiers, fast lookups
- **Secondary indexes**: Foreign keys, frequently filtered columns
- **Composite indexes**: Multiple columns for complex queries
- **Best practices**: Index columns used in WHERE, JOIN, ORDER BY

### Caching

- **In-memory cache**: Frequently accessed data (Redis, Memcached)
- **Query result cache**: Cache expensive query results
- **Reference data cache**: Cache lookup tables, dimensions
- **Best practices**: Cache hot data, set TTL, invalidate on updates

### Parallelization

- **Parallel tasks**: Run independent tasks simultaneously
- **Partition parallelism**: Process partitions in parallel
- **Worker pools**: Scale workers based on workload
- **Best practices**: Balance parallelism vs resource contention

### Pushdown Optimization

- **Filter pushdown**: Push filters to source (reduce data transfer)
- **Projection pushdown**: Select only needed columns
- **Aggregation pushdown**: Aggregate at source when possible
- **Best practices**: Minimize data movement, leverage source capabilities

### Compression

- **File compression**: Compress files (Parquet, Gzip, Snappy)
- **Columnar formats**: Parquet, ORC (columnar storage, better compression)
- **Best practices**: Balance compression ratio vs CPU cost

---

> **Note**: For governance and quality practices, see [Data Management](data_management.md). For analytics/BI layer patterns, see [Analytics Engineering](analytics_engineering.md). For Microsoft-specific implementation, see [Microsoft Data Platform Implementation](ms_architecture.md).

