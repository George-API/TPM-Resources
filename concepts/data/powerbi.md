# Analytics Engineering for Power BI Optimization

**Scope**: Optimizing analytics engineering specifically for Power BI reports: data modeling, performance, and best practices.

**Purpose**: Use this when preparing data models and transformations for Power BI consumption. For general analytics engineering patterns, see [Analytics Engineering](analytics_eng.md). For Power BI reporting practices, see [Enterprise Reporting](../project_management/reporting.md).

## Table of Contents

- [1. Data Modeling for Power BI](#1-data-modeling-for-power-bi)
- [2. Power BI Connection Modes](#2-power-bi-connection-modes)
- [3. Performance Optimization](#3-performance-optimization)
- [4. Data Preparation Best Practices](#4-data-preparation-best-practices)
- [5. Semantic Model Optimization](#5-semantic-model-optimization)
- [6. Fabric/OneLake Integration](#6-fabriconelake-integration)

---

## 1. Data Modeling for Power BI

### Star Schema (Recommended)

**Structure**
- **Fact tables**: Transactional data, metrics, measures (sales, events, transactions)
- **Dimension tables**: Descriptive attributes (products, customers, dates, geography)
- **Relationships**: One-to-many (dimension → fact), single direction

**Benefits for Power BI**
- Fast query performance, intuitive for report authors
- Efficient DAX calculations, better compression
- Clear data model visualization, easier to understand

**Best Practices**
- Single grain per fact table (define fact table grain clearly)
- Denormalized dimensions (fewer joins, better performance)
- Surrogate keys for dimensions (integer keys, not business keys)
- Avoid bi-directional relationships (use single direction, mark as filter)
- Limit fact table columns (only necessary measures, foreign keys)

### Date Dimension

**Essential for Power BI**
- Create dedicated date dimension table (not just date column)
- Include: Date, Year, Quarter, Month, Week, Day of Week, Fiscal periods
- Mark as date table in Power BI (enables time intelligence functions)
- Use continuous date range (no gaps), cover all dates in fact tables

**Best Practices**
- Pre-calculate date attributes (year, quarter, month, week)
- Include fiscal calendar if applicable
- Add flags (IsWeekend, IsHoliday, IsMonthEnd)
- Use integer date key (YYYYMMDD format) for joins

### Dimension Design

**Denormalization**
- Flatten hierarchies into single dimension table (country → region → city in one table)
- Include all commonly used attributes (avoid multiple dimension tables)
- Balance: storage vs query performance (denormalize for Power BI)

**Slowly Changing Dimensions (SCD)**
- **Type 1**: Overwrite (current value only) - simplest, no history
- **Type 2**: Full history (new row per change) - track all changes, use effective date ranges
- **Type 3**: Limited history (previous value column) - track one previous value
- Choose based on business requirements, Power BI handles Type 2 well

### Fact Table Design

**Grain Definition**
- Define clear grain (one row = one transaction, one event, one day, etc.)
- Document grain clearly, ensure consistency
- Avoid mixed grains in same fact table

**Measures vs Columns**
- Store additive measures in fact table (quantity, amount, count)
- Calculate non-additive measures in Power BI (ratios, percentages)
- Avoid storing pre-calculated percentages, ratios (calculate in DAX)

**Partitioning**
- Partition by date (monthly, quarterly) for large fact tables
- Enables incremental processing, better query performance
- Use partition elimination in queries

---

## 2. Power BI Connection Modes

### Direct Lake (Fabric/OneLake)

**Best Performance**
- Query OneLake Delta tables directly (no import, no DirectQuery overhead)
- Requires Fabric workspace, Delta format, proper partitioning
- Fastest option when available, real-time data access

**Optimization**
- Use Delta format in OneLake (Parquet with Delta log)
- Partition tables by date (monthly recommended)
- Optimize Delta files (Z-ordering, file size, compaction)
- Use V-Order for better compression (Fabric feature)

### Import Mode

**Fast Queries, Limited Size**
- Data imported into Power BI (compressed, in-memory)
- Fast query performance, works offline
- Limited by dataset size (varies by Power BI license)

**Optimization**
- Minimize columns (only needed columns)
- Use appropriate data types (avoid text for numbers)
- Remove unnecessary rows (filter at source)
- Use aggregations for large datasets
- Incremental refresh for large tables

### DirectQuery Mode

**Real-Time, Larger Datasets**
- Live queries to source (Fabric Warehouse, SQL Server, etc.)
- Real-time data, no size limit
- Slower queries, requires optimized source

**Optimization**
- Optimize source queries (indexes, statistics, query plans)
- Minimize query complexity (avoid complex joins in DirectQuery)
- Use aggregations to reduce DirectQuery load
- Limit DirectQuery tables (use import for dimensions when possible)

### Composite Mode

**Hybrid Approach**
- Mix import + DirectQuery (import dimensions, DirectQuery facts)
- Best of both worlds: fast dimensions, real-time facts
- Use for large fact tables with smaller dimensions

---

## 3. Performance Optimization

### Aggregations

**Pre-Aggregated Tables**
- Create aggregate tables for common queries (daily, monthly summaries)
- Power BI automatically uses aggregates when possible
- Reduces query load, improves performance

**Best Practices**
- Aggregate by common dimensions (date, product, region)
- Match aggregation level to report needs (daily vs monthly)
- Use incremental processing for aggregates
- Document aggregation strategy

### Columnar Storage Optimization

**Data Types**
- Use smallest appropriate data type (integer vs bigint, date vs datetime)
- Avoid text for numeric data (use numeric types)
- Use date/time types (not text dates)

**Compression**
- Power BI compresses data automatically (columnar storage)
- Sorted data compresses better (sort by date, key columns)
- Remove unnecessary columns (reduces storage, improves performance)

### Partitioning Strategy

**Date Partitioning**
- Partition fact tables by date (monthly, quarterly)
- Enables incremental refresh, partition elimination
- Align partitions with report refresh schedules

**Best Practices**
- Use consistent partition size (monthly recommended)
- Include partition metadata (partition date, load date)
- Monitor partition sizes, rebalance if needed

### Query Optimization

**Reduce Data Volume**
- Filter at source (not in Power BI)
- Use WHERE clauses in source queries
- Limit date ranges, filter unnecessary data
- Use aggregations for summary reports

**Minimize Joins**
- Denormalize dimensions (fewer joins)
- Pre-join in transformation layer when possible
- Use single-direction relationships

---

## 4. Data Preparation Best Practices

### Data Quality

**Cleaning**
- Handle nulls appropriately (replace, filter, or keep based on business logic)
- Standardize formats (dates, text, codes)
- Validate data types, ranges, business rules
- Document data quality issues, handling strategies

**Deduplication**
- Remove duplicates at transformation layer (not in Power BI)
- Use deterministic deduplication logic
- Document deduplication rules

### Transformation Patterns

**Calculated Columns vs Measures**
- **Calculated columns**: Pre-computed, stored in model (use for filtering, grouping)
- **Measures**: Computed at query time (use for aggregations, calculations)
- Prefer measures for calculations (smaller model, more flexible)
- Use calculated columns only when needed for filtering/grouping

**Hierarchies**
- Create hierarchies in Power BI (not in data model)
- Use dimension attributes for hierarchy levels
- Pre-calculate hierarchy paths if needed (for complex hierarchies)

### Incremental Processing

**Incremental Refresh**
- Process only new/changed data (not full refresh)
- Use date-based incremental refresh (daily, weekly)
- Define incremental refresh policy (lookback period, refresh frequency)

**Best Practices**
- Use date columns for incremental logic
- Handle late-arriving data (backfill strategy)
- Monitor incremental refresh performance
- Document incremental refresh strategy

---

## 5. Semantic Model Optimization

### Relationships

**Relationship Design**
- Single-direction relationships (dimension → fact)
- Use integer keys (not text keys) for better performance
- Mark relationships as filter (not both directions)
- Avoid circular dependencies

**Cardinality**
- One-to-many (dimension → fact) - most common
- Many-to-one (fact → dimension) - same as one-to-many, different direction
- One-to-one - rare, usually indicates design issue
- Many-to-many - use bridge tables, avoid if possible

### DAX Optimization

**Measure Design**
- Write efficient DAX (avoid row-by-row calculations)
- Use aggregation functions (SUM, COUNT, AVERAGE) not iterators when possible
- Cache intermediate calculations (variables)
- Avoid calculated columns in measures (use measures)

**Best Practices**
- Use CALCULATE for context modification
- Use FILTER sparingly (can be slow)
- Use time intelligence functions (YTD, MTD, same period last year)
- Test DAX performance, optimize slow measures

### Hierarchies & Drill-Down

**Hierarchy Design**
- Create hierarchies in Power BI (date, geography, product)
- Use dimension attributes for levels
- Enable drill-down in visuals (hierarchies enable this)

**Best Practices**
- Limit hierarchy depth (3-4 levels recommended)
- Use meaningful level names
- Test drill-down performance

---

## 6. Fabric/OneLake Integration

### Direct Lake Setup

**Requirements**
- Fabric workspace with OneLake
- Delta format tables (Parquet with Delta log)
- Proper partitioning (date-based recommended)
- V-Order enabled (Fabric feature for better compression)

**Best Practices**
- Use Gold layer for Power BI (curated, business-ready)
- Optimize Delta files (Z-ordering, file size, compaction)
- Partition by date (monthly recommended)
- Monitor Direct Lake query performance

### Data Refresh

**Incremental Refresh**
- Use incremental refresh for large tables
- Define refresh policy (lookback period, refresh frequency)
- Handle late-arriving data
- Monitor refresh performance

**Scheduled Refresh**
- Schedule refreshes during off-peak hours
- Coordinate with data pipeline schedules
- Use Power Automate for complex refresh logic
- Monitor refresh failures, set up alerts

### Governance

**Certified Datasets**
- Promote datasets to certified (governance-approved)
- Document dataset purpose, owners, refresh schedule
- Use certified datasets in reports (trusted source)

**Lineage**
- Document data lineage (source → transformation → Power BI)
- Use Microsoft Purview for lineage tracking
- Document transformations, business logic

---

> **Note**: For general analytics engineering patterns, see [Analytics Engineering](analytics_eng.md). For Power BI reporting practices, see [Enterprise Reporting](../project_management/reporting.md). For Fabric architecture, see [Fabric Architecture](../cloud/fabric_architecture.md).

