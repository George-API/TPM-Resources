# Analytics Engineering Patterns — Platform-Agnostic

**Scope**: Platform-agnostic patterns for analytics engineering: transformation layer, semantic layer, BI-ready modeling, and metric definitions.

**Purpose**: Use this when building the analytics/BI consumption layer. For pipeline patterns, see Data Engineering. For governance, see Data Management.

## Table of Contents

- [1. Transformation Layer](#1-transformation-layer)
- [2. Semantic Layer](#2-semantic-layer)
- [3. BI-Ready Modeling](#3-bi-ready-modeling)
- [4. Metric Definitions & Calculations](#4-metric-definitions--calculations)
- [5. Data Product Patterns](#5-data-product-patterns)
- [6. Testing Patterns](#6-testing-patterns)
- [7. Documentation & Lineage](#7-documentation--lineage)
- [8. Version Control & CI/CD](#8-version-control--cicd)

---

## 1. Transformation Layer

### SQL-Based Transformations

- **dbt (data build tool)**: SQL-based transformation framework
- **Pattern**: Write SQL, dbt compiles to executable transformations
- **Benefits**: Version control, testing, documentation, modularity
- **Best practices**:
  - Modular SQL (models, macros, tests)
  - Reusable macros for common patterns
  - Incremental models for performance

### Transformation Patterns

- **Staging models**: Raw data → cleaned staging tables
- **Intermediate models**: Staging → intermediate transformations
- **Marts**: Intermediate → business-ready marts (star schemas, wide tables)
- **Pattern**: Staging → Intermediate → Marts (layered approach)

### Incremental Models

- **Incremental strategy**: Only process new/changed records
- **Patterns**:
  - `append`: Add new records only
  - `merge`: Upsert (insert or update)
  - `delete+insert`: Replace partition
- **Best practices**: Use unique keys, handle late arrivals, backfill strategy

### Materialization Strategies

- **Table**: Full table materialization (rebuild each run)
- **View**: Virtual table (computed on query)
- **Incremental**: Only process new/changed data
- **Ephemeral**: CTE, not materialized (used in other models)
- **Decision factors**: Query frequency, data volume, freshness requirements

### Modular SQL

- **Models**: Reusable SQL transformations
- **Macros**: Parameterized SQL snippets (DRY principle)
- **Sources**: Define source tables (single source of truth)
- **Seeds**: CSV files for reference data
- **Best practices**: Build models incrementally, test each layer

---

## 2. Semantic Layer

### Metrics Layer

- **Metric definitions**: Centralized metric definitions (revenue, users, conversion rate)
- **Pattern**: Define once, use everywhere
- **Benefits**: Consistency, single source of truth, easier maintenance
- **Tools**: dbt metrics, Looker LookML, Cube.dev, MetricFlow

### Metric Types

- **Simple metrics**: Count, sum, average, min, max
- **Ratio metrics**: Division of two metrics (conversion rate, average order value)
- **Derived metrics**: Calculated from other metrics
- **Time-based metrics**: Metrics over time (daily active users, monthly revenue)

### Dimensions & Hierarchies

- **Dimensions**: Categorical attributes (date, product, customer, region)
- **Hierarchies**: Roll-up relationships (country → region → city)
- **Pattern**: Define dimensions once, use across metrics
- **Best practices**: Consistent naming, clear hierarchies, time dimensions

### Aggregations

- **Pre-aggregation**: Pre-compute common aggregations (daily, monthly)
- **On-the-fly aggregation**: Compute at query time
- **Decision factors**: Query frequency, data volume, freshness needs
- **Best practices**: Balance storage cost vs query performance

### Metric Governance

- **Ownership**: Assign metric owners (data stewards)
- **Documentation**: Define metric, calculation, use cases, examples
- **Validation**: Test metrics against source systems
- **Versioning**: Track metric changes, deprecation lifecycle

---

## 3. BI-Ready Modeling

### Star Schema

- **Fact tables**: Transactional data (sales, events, metrics)
- **Dimension tables**: Descriptive attributes (products, customers, dates)
- **Pattern**: Fact table (grain) + dimension tables (attributes)
- **Benefits**: Fast queries, intuitive for analysts, BI tool friendly
- **Best practices**: 
  - Single grain per fact table
  - Denormalized dimensions (fewer joins)
  - Surrogate keys for dimensions

### Snowflake Schema

- **Normalized dimensions**: Dimensions have sub-dimensions
- **Pattern**: Fact → Dimension → Sub-dimension
- **Use case**: When dimensions are large and normalized
- **Trade-off**: More joins, less storage, more complex

### Wide Tables (One Big Table)

- **Single table**: All attributes in one denormalized table
- **Pattern**: Flatten all dimensions into fact table
- **Use case**: Simple analytics, small datasets, ad-hoc analysis
- **Trade-off**: Simpler queries, but redundant data, harder to maintain

### Aggregate Tables

- **Pre-aggregated**: Pre-compute common aggregations
- **Patterns**:
  - Daily/monthly summaries
  - Roll-ups by dimension (by product, by region)
- **Benefits**: Faster queries, reduced compute cost
- **Best practices**: Balance freshness vs performance, incremental updates

### Slowly Changing Dimensions (SCD)

- **Type 1**: Overwrite (no history)
- **Type 2**: Full history (new row for each change)
- **Type 3**: Limited history (previous value column)
- **Use case**: Track dimension changes over time
- **Best practices**: Choose SCD type based on business needs

---

## 4. Metric Definitions & Calculations

### Metric Naming

- **Consistent naming**: `metric_name__time_grain` (e.g., `revenue__daily`)
- **Pattern**: Clear, descriptive, consistent
- **Best practices**: Avoid abbreviations, use business terminology

### Calculation Patterns

- **Simple aggregations**: SUM, COUNT, AVG, MIN, MAX
- **Window functions**: Running totals, moving averages, rankings
- **Conditional aggregations**: SUM(CASE WHEN ...), COUNT(DISTINCT ...)
- **Ratio calculations**: Divide two metrics (with null handling)

### Time-Based Metrics

- **Period comparisons**: YoY, MoM, WoW (year-over-year, month-over-month)
- **Cumulative metrics**: Running totals, year-to-date
- **Growth rates**: Percentage change, absolute change
- **Best practices**: Handle time zones, business days, fiscal calendars

### Metric Validation

- **Source reconciliation**: Compare metrics to source systems
- **Reasonableness checks**: Validate against expected ranges
- **Trend analysis**: Detect anomalies, sudden changes
- **Best practices**: Automated validation, alert on discrepancies

### Metric Documentation

- **Definition**: What the metric measures
- **Calculation**: How it's calculated (formula, SQL)
- **Use cases**: When to use this metric
- **Examples**: Sample values, expected ranges
- **Ownership**: Who owns this metric

---

## 5. Data Product Patterns

### Data Product Definition

- **Self-contained**: Dataset with clear purpose, contract, documentation
- **Components**:
  - Data (tables, views, APIs)
  - Schema (structure, types, constraints)
  - Documentation (description, examples, changelog)
  - Access model (permissions, roles, RLS)
  - SLAs (freshness, availability, quality)

### Data Product Types

- **Analytical datasets**: BI-ready tables, star schemas
- **Operational datasets**: Real-time data products, APIs
- **ML datasets**: Feature stores, training datasets
- **Reference datasets**: Master data, lookup tables

### Data Product Lifecycle

- **Draft**: Exploratory, not production-ready
- **Verified**: Tested, limited SLA
- **Certified**: Production-ready, full SLA, monitored
- **Deprecated**: Being phased out, migration path provided

### Data Product Contracts

- **Schema contract**: Column names, types, constraints
- **Semantic contract**: Business meaning, definitions
- **SLA contract**: Freshness, availability, quality targets
- **Versioning**: Backward compatibility, deprecation policy

### Data Product Discovery

- **Catalog**: Searchable catalog of data products
- **Tags**: Domain tags, sensitivity labels, certification status
- **Documentation**: Getting started guides, examples, changelog
- **Lineage**: Upstream sources, downstream consumers

---

## 6. Testing Patterns

### Data Tests

- **Uniqueness**: Ensure unique keys, no duplicates
- **Not null**: Required fields are not null
- **Accepted values**: Values in expected set (enums)
- **Relationships**: Foreign key integrity, referential integrity
- **Custom tests**: Business rule validation

### Schema Tests

- **Column types**: Validate data types
- **Column presence**: Required columns exist
- **Column count**: Expected number of columns
- **Best practices**: Test at each transformation layer

### Custom Tests

- **Business rules**: Custom validation logic
- **Pattern**: Write SQL that returns failing records
- **Examples**: 
  - Revenue should be positive
  - Dates should be in valid range
  - Sums should reconcile to source

### Test Coverage

- **Unit tests**: Test individual models/transformations
- **Integration tests**: Test end-to-end transformations
- **Data quality tests**: Test data quality rules
- **Best practices**: Test critical paths, automate in CI/CD

### Test Execution

- **Development**: Run tests locally during development
- **CI/CD**: Run tests before deployment
- **Production**: Monitor tests in production, alert on failures
- **Best practices**: Fast feedback, clear error messages

---

## 7. Documentation & Lineage

### Model Documentation

- **Description**: What the model does, purpose, use cases
- **Columns**: Column descriptions, data types, examples
- **Dependencies**: Upstream models, downstream consumers
- **Examples**: Sample queries, common use cases
- **Best practices**: Keep documentation up-to-date, use markdown

### Lineage Documentation

- **Upstream lineage**: Where data comes from (sources, upstream models)
- **Downstream lineage**: Where data goes (downstream models, BI tools)
- **Column-level lineage**: Track column transformations
- **Best practices**: Automated lineage where possible, document manually where needed

### Changelog

- **Schema changes**: Column additions, removals, type changes
- **Logic changes**: Calculation changes, business rule updates
- **Deprecations**: Models being phased out, migration paths
- **Best practices**: Version control, clear dates, migration guides

### Getting Started Guides

- **Quick start**: How to use this data product
- **Sample queries**: Common query patterns
- **Joins**: How to join with other tables
- **Pitfalls**: Common mistakes, edge cases
- **Best practices**: Make it easy for new users

---

## 8. Version Control & CI/CD

### Version Control

- **Git workflow**: Branch, commit, PR, merge
- **Best practices**: 
  - Small, focused commits
  - Clear commit messages
  - PR reviews before merge
- **Pattern**: Feature branch → PR → Review → Merge to main

### CI/CD Pipeline

- **Build**: Compile transformations, validate SQL
- **Test**: Run data tests, schema tests, custom tests
- **Deploy**: Deploy to staging, then production
- **Best practices**: 
  - Fast feedback (fail fast)
  - Automated testing
  - Staged deployments

### Environment Strategy

- **Development**: Local development, testing
- **Staging**: Pre-production validation
- **Production**: Live environment, users
- **Best practices**: 
  - Test in staging before production
  - Use feature flags for gradual rollout
  - Monitor production deployments

### Deployment Patterns

- **Full refresh**: Rebuild entire model (small tables)
- **Incremental**: Only process new/changed data (large tables)
- **Blue-green**: Deploy to new environment, switch traffic
- **Rollback**: Ability to rollback to previous version
- **Best practices**: 
  - Test rollback procedures
  - Monitor after deployment
  - Gradual rollout for large changes

### Change Management

- **Breaking changes**: Schema changes, calculation changes
- **Deprecation**: Announce → Dual-run → Cutover → Retire
- **Communication**: Notify consumers of changes
- **Best practices**: 
  - Backward compatibility when possible
  - Clear migration paths
  - Sufficient notice period

---

> **Note**: For pipeline patterns, see [Data Engineering](data_engineering.md). For governance and quality practices, see [Data Management](data_management.md). For Microsoft-specific implementation (Power BI, Fabric), see [Microsoft Data Platform Implementation](ms_architecture.md).

