# Azure Databricks Architecture Patterns

**Scope**: Azure Databricks-centric enterprise data platform implementation (ingest → lake → serving → BI/ML).

## Table of Contents

- [Core Stack](#core-stack)
- [1. Reference Architecture (Medallion Lakehouse)](#1-reference-architecture-medallion-lakehouse)
- [2. Azure Databricks Patterns](#2-azure-databricks-patterns)
- [3. Security & Governance](#3-security--governance)
- [4. Reliability & Data Freshness](#4-reliability--data-freshness)
- [5. Data Modeling & Serving](#5-data-modeling--serving)
- [6. Cost & Performance](#6-cost--performance)
- [7. Default Controls Checklist](#7-default-controls-checklist)

---

## Core Stack

**Typical Databricks stack:**

- **Ingestion/Orchestration**: Azure Data Factory (ADF) / Databricks Jobs / Databricks Workflows
- **Streaming**: Event Hubs → Databricks Structured Streaming / Delta Live Tables
- **Storage/Lake**: Azure Data Lake Storage Gen2 (ADLS Gen2), Delta Lake (native)
- **Processing**: Databricks Workspace (Spark clusters, Photon engine)
- **Serving**: Databricks SQL (serverless/provisioned SQL warehouses)
- **Semantic/BI**: Power BI (DirectQuery to Databricks SQL), Tableau, other BI tools
- **ML/AI**: MLflow (model lifecycle), Databricks ML runtime, Feature Store
- **Governance**: Unity Catalog (unified governance)
- **Security/Monitoring**: Entra ID, Key Vault, Defender for Cloud, Databricks monitoring, Azure Monitor

---

## 1. Reference Architecture (Medallion Lakehouse)

### A) Sources

- SaaS (Dynamics, ServiceNow, etc.), on-prem DBs, files, APIs, IoT/telemetry

### B) Ingestion Layer

- **Batch**: Azure Data Factory (copy + transforms) → Bronze; Databricks Jobs → Bronze
- **CDC**: source CDC → landing → Bronze (or event stream via Delta Live Tables)
- **Streaming**: Event Hubs → Databricks Structured Streaming / Delta Live Tables → Bronze/Silver

### C) Lakehouse Layers (Medallion)

- **Bronze**: raw/append-only, immutable, source-aligned partitions
- **Silver**: cleaned/standardized, deduped, schema-enforced, conformed dimensions
- **Gold**: curated business entities / aggregates for analytics + BI serving

### D) Serving

- **Warehouse/SQL**: Databricks SQL (serverless or provisioned) for BI-friendly consumption
- **APIs**: lightweight serving via App Service/Functions if operational consumers need data products
- **ML Serving**: MLflow model serving, Databricks Model Serving

### E) Governance/Operations

- Unity Catalog for catalog + lineage + access control
- Databricks monitoring + Azure Monitor for pipeline/run telemetry, freshness, failures
- Data quality gates + quarantine + replay tooling

---

## 2. Azure Databricks Patterns

### ADLS Gen2 (Storage)

- **Hierarchical namespace**: enables directory semantics and POSIX-like access
- **Delta Lake**: native Delta format with ACID transactions, time travel, schema evolution
- **Partitioning**: by date/tenant/source; optimize file sizes (128MB-1GB target)
- **Access control**: Unity Catalog + RBAC + ACLs for fine-grained permissions
- **Lifecycle management**: hot/cool/archive tiers, automatic tiering
- **Immutable storage**: WORM (Write Once, Read Many) for compliance scenarios
- **Soft delete**: recover deleted blobs within retention period

### Medallion Architecture Implementation

- **Bronze (Raw)**: 
  - ADLS location: `{container}/bronze/{source}/{table}/`
  - Delta tables in Unity Catalog: `catalog.schema.bronze_{source}_{table}`
  - Append-only; immutable; source-aligned partitions
  - Schema-on-read; minimal validation
- **Silver (Cleaned)**:
  - ADLS location: `{container}/silver/{domain}/{table}/`
  - Delta tables in Unity Catalog: `catalog.schema.silver_{domain}_{table}`
  - Deduplicated; standardized; schema-enforced
  - Quality checks; quarantine bad records
- **Gold (Curated)**:
  - ADLS location: `{container}/gold/{domain}/{table}/`
  - Delta tables in Unity Catalog: `catalog.schema.gold_{domain}_{table}`
  - Business entities; star schemas; aggregates
  - Certified datasets; documented semantics

### Databricks Workspace

- **Workspace organization**: folders, notebooks, libraries, clusters, jobs
- **Notebooks**: Python, Scala, R, SQL notebooks for interactive development
- **Repos**: Git integration (Azure DevOps, GitHub) for version control
- **Libraries**: install Python/Scala/R packages; cluster-scoped or notebook-scoped
- **Cluster management**: shared/job clusters; auto-termination; cluster policies
- **Workspace access control**: workspace-level permissions, folder-level permissions

### Databricks Compute

- **All-purpose clusters**: interactive development, ad-hoc analysis
- **Job clusters**: automated jobs, ephemeral compute
- **SQL warehouses**: serverless (auto-scaling) or provisioned (fixed size) for SQL workloads
- **Photon engine**: accelerated SQL engine for faster query performance
- **Cluster configurations**: 
  - Spark runtime versions (LTS vs latest)
  - Instance types (compute-optimized, memory-optimized)
  - Autoscaling (min/max workers)
  - Auto-termination (idle timeout)
- **Cluster policies**: enforce instance types, auto-termination, cluster limits per user/group

### Delta Live Tables (DLT)

- **Declarative pipelines**: define transformations as SQL/Python; automatic orchestration
- **Incremental processing**: automatic change data capture (CDC) handling
- **Data quality**: expectations framework (expect, expect_or_fail, expect_or_drop)
- **Unified batch/streaming**: same code for batch and streaming workloads
- **Automatic retries**: built-in fault tolerance and retry logic
- **Lineage tracking**: automatic lineage in Unity Catalog

### Databricks Jobs & Workflows

- **Jobs**: scheduled or triggered notebook/jar/Python script execution
- **Workflows**: multi-task jobs with dependencies, conditional logic, retries
- **Triggers**: schedule-based (cron), event-based (Event Grid), manual
- **Job clusters**: ephemeral clusters for cost optimization
- **Job parameters**: parameterize jobs for reusability across environments
- **Job monitoring**: job run history, alerts on failures, retry policies
- **Git integration**: run jobs from Git repos (branch/tag/commit)

### Databricks SQL

- **SQL warehouses**: serverless (auto-scaling, pay-per-query) or provisioned (fixed size)
- **Query editor**: interactive SQL queries on Delta tables
- **Dashboards**: built-in visualization and dashboards
- **Alerts**: query-based alerts on data conditions
- **Query history**: performance insights, query optimization recommendations
- **Photon acceleration**: automatic acceleration for compatible queries
- **Direct lake access**: query Delta tables directly without import

### Structured Streaming

- **Streaming sources**: Event Hubs, Kafka, ADLS, Delta tables
- **Streaming sinks**: Delta tables, Event Hubs, ADLS
- **Checkpointing**: fault-tolerant stream processing with checkpoint locations
- **Trigger modes**: continuous (low latency) or micro-batch (balanced)
- **Watermarks**: late data handling with watermark windows
- **Stream-stream joins**: join multiple streams with time constraints

### MLflow Integration

- **Experiment tracking**: log parameters, metrics, artifacts
- **Model registry**: versioned model storage, stage transitions (None → Staging → Production)
- **Model serving**: REST API for model inference (Databricks Model Serving)
- **Feature Store**: centralized feature management, point-in-time lookups
- **ML runtime**: pre-configured ML libraries (scikit-learn, XGBoost, PyTorch, etc.)

### Data Networking (Databricks)

- **VNet injection**: deploy Databricks workspace in customer VNet (secure cluster connectivity)
- **Private endpoints**: Private endpoints for workspace access (control plane)
- **Secure cluster connectivity**: no public IPs for clusters (VNet injection)
- **Data plane isolation**: separate subnets for data ingestion vs serving
- **Egress control**: NAT Gateway for stable outbound IPs (source systems allowlists)
- **Service endpoints**: use service endpoints for PaaS services when private endpoints not available

> **Note**: For general networking patterns, see [Cloud Architecture](architecture.md). For OSI troubleshooting, see [OSI Model](../software/osi.md).

---

## 3. Security & Governance

### Identity

- Entra ID everywhere; Managed Identity for Databricks workspace and clusters
- PIM for privileged roles; workspace-level and cluster-level permissions
- Service principals for automated jobs and CI/CD

### Data access controls

- **Storage**: Unity Catalog + RBAC + ACLs (ADLS Gen2) carefully designed (least privilege)
- **Serving**: Unity Catalog RLS for row-level security; column-level security for sensitive columns
- **Keys/secrets**: Databricks Secrets (Key Vault-backed) or Key Vault directly; CMK when required; rotate certificates

### Governance

- **Unity Catalog**:
  - unified catalog across workspaces (multi-workspace governance)
  - catalog + schema + table hierarchy
  - lineage for pipelines and lakehouse assets
  - classification labels for sensitive fields
  - data contracts enforcement (schema validation)
  - external locations for ADLS/S3 access
- **Azure Policy**:
  - deny public endpoints on data stores, enforce private link, enforce tagging, restrict regions/SKUs
- **Resource locks**: prevent accidental deletion of critical resources
- **Workspace settings**: IP access lists, audit logs, workspace-level policies

> **Note**: For data management concepts (operating model, quality, integrity), see [Data Governance](../data/governance.md), [Data Operations](../data/operations.md), and [Data Security](../data/security.md).

---

## 4. Reliability & Data Freshness

> **Note**: For data SLO concepts (what they are, why they matter), see [Data Operations - Reliability](../data/operations.md#8-reliability--data-sre).

### Implementing Data SLOs (Databricks)

- **Freshness SLO**: Monitor Databricks job completion times; alert on SLA breaches
  - Use Databricks job run metrics + Azure Monitor / Log Analytics queries
  - Alert when Gold partition update exceeds SLA window
- **Completeness SLO**: Compare record counts vs source control totals
  - Use data quality checks in Databricks jobs (Delta Live Tables expectations); publish metrics to Azure Monitor
- **Accuracy SLO**: Quality check pass rates
  - Integrate Great Expectations or custom checks; publish to dashboard
- **Availability SLO**: Databricks SQL warehouse uptime; query success rates

### Operational patterns

- **Watermarking**:
  - track high-water mark per source/table; store in control table (Delta table or Azure SQL)
- **Idempotency**:
  - reruns should not duplicate (merge/upsert patterns in Delta tables)
  - use Delta Lake `MERGE` statements for idempotent writes
- **Backpressure**:
  - autoscale clusters on queue depth; throttle upstream if downstream is hot
  - use cluster autoscaling for variable workloads
- **Quarantine**:
  - bad rows → quarantine storage (separate Delta table or ADLS container) + defect metrics + triage workflow
  - use Delta Live Tables `expect_or_drop` for automatic quarantine

---

## 5. Data Modeling & Serving

### Gold Layer Design

- **Star schemas**: fact tables + dimension tables for BI (Power BI optimized)
- **Curated wide tables**: denormalized where justified (fewer joins, faster queries)
- **Partitioning**: by date for time-series; by tenant for multi-tenant
- **Z-ordering**: optimize Delta table layout for query patterns (co-locate related data)
- **Bloom filters**: accelerate point lookups on Delta tables

### Data Products

- **Databricks data products**: publish Gold tables as certified datasets in Unity Catalog
- **Contracts**: schema, semantics, keys, SLAs defined in Unity Catalog
- **Documentation**: table comments, column descriptions, sample queries, changelog
- **Sharing**: Delta Sharing for secure data sharing across organizations

> **Note**: For data contract concepts and operating model, see [Data Governance](../data/governance.md).

---

## 6. Cost & Performance

- **Partitioning strategy**:
  - by date (and sometimes tenant/source) for lake
  - avoid over-partitioning (too many small partitions)
- **File sizing**:
  - avoid many tiny files; compact/optimize Silver/Gold regularly
  - use `OPTIMIZE` and `ZORDER` commands on Delta tables
- **Avoid moving data unnecessarily**:
  - pushdown filters, incremental loads, CDC where possible
  - use Delta Lake change data feed for incremental processing
- **Choose the right compute**:
  - ADF for orchestration + movement
  - Databricks clusters for heavy transforms (Spark)
  - Databricks SQL for serving + BI transforms
  - Delta Live Tables for declarative pipelines
- **Cluster optimization**:
  - use job clusters (ephemeral) instead of all-purpose for scheduled jobs
  - enable autoscaling for variable workloads
  - set auto-termination for all-purpose clusters
  - use cluster pools for faster cluster startup
  - right-size instance types (compute vs memory optimized)
- **Photon engine**:
  - automatic acceleration for SQL workloads (serverless SQL warehouses)
  - faster query performance without code changes
- **Delta Lake optimization**:
  - `OPTIMIZE`: compact small files into larger files
  - `ZORDER`: co-locate related data for faster queries
  - `VACUUM`: remove old files (respect retention period)
  - table maintenance jobs for regular optimization

### Disaster Recovery & High Availability

- **ADLS Gen2**: geo-redundant storage (GRS/RA-GRS) for cross-region backup; soft delete for point-in-time recovery
- **Databricks workspace**: workspace backup (notebooks, jobs, cluster configs) via Git repos; cross-region workspace for DR
- **Delta tables**: Delta Lake time travel for point-in-time recovery; cross-region replication for critical tables
- **ADF pipelines**: store definitions in Git (Azure DevOps/GitHub); parameterize connection strings for DR failover
- **Event Hubs**: geo-disaster recovery (paired namespaces) for streaming workloads
- **RTO/RPO targets**: define recovery objectives per workload tier; test DR procedures regularly

### CI/CD for Data Pipelines

- **Source control**: Databricks Repos (Git integration) for notebooks and code
- **Deployment**: Azure DevOps pipelines or GitHub Actions for automated deployment
- **Environment promotion**: dev → test → prod with parameterized jobs and cluster configs
- **Testing**: unit tests for data transformations; integration tests for job runs; data quality tests in staging
- **Approval gates**: manual approvals for production deployments; automated quality gates
- **Databricks Asset Bundles**: declarative deployment of Databricks assets (workspaces, jobs, notebooks)

### Monitoring & Alerting

- **Job monitoring**: Databricks job run metrics; Azure Monitor integration for custom metrics
- **Data freshness alerts**: alert when job completion exceeds SLA; monitor partition update timestamps
- **Cost alerts**: budget alerts per subscription/resource group; cost anomaly detection
- **Performance monitoring**: query performance insights (Databricks SQL); Spark job metrics (cluster UI)
- **Health dashboards**: Azure Monitor workbooks for custom dashboards; Power BI for operational reporting
- **Databricks monitoring**: workspace-level metrics, cluster utilization, job success rates

### Error Handling & Retry Strategies

- **Job retry policies**: configure retry count and interval per job; exponential backoff for transient failures
- **Dead letter queues**: Event Hubs capture failed messages; Service Bus dead-letter queues for processing failures
- **Job error handling**: try-catch blocks in notebooks; conditional logic in workflows
- **Idempotent operations**: use Delta Lake `MERGE` patterns; watermark-based incremental loads prevent duplicates on retry
- **Notification**: email/Slack alerts on job failures; integration with ITSM tools (ServiceNow)
- **Delta Live Tables**: automatic retries with configurable retry policies

### Data Lifecycle & Archiving

- **ADLS lifecycle policies**: automatic tiering (hot → cool → archive); delete old partitions after retention period
- **Bronze retention**: retain raw data per compliance requirements (typically 7-90 days)
- **Silver retention**: retain cleaned data longer (typically 1-3 years)
- **Gold retention**: retain curated data indefinitely or per business requirements
- **Archive strategy**: move cold data to Archive tier; restore from archive when needed (hours to days)
- **Delta Lake VACUUM**: remove old Delta files (respect retention period; default 7 days)

---

## 7. "Default Controls" Checklist

### Network

- VNet injection + secure cluster connectivity
- Private endpoints + Private DNS Zones
- Deny public network access for data stores
- Central egress via Firewall/NAT

### Identity

- Managed Identity everywhere possible
- Least privilege RBAC + PIM for admins
- Service principals for automated jobs

### Data hygiene

- Bronze append-only; Silver validated; Gold curated
- Schema validation + quarantine (Delta Live Tables expectations)
- Watermarks + idempotent reruns (Delta MERGE)
- Unity Catalog schema enforcement

### Observability

- Job run telemetry + alerts (Databricks monitoring + Azure Monitor)
- Freshness + backlog dashboards
- Cost per job/cluster + anomaly alerts
- Application Insights for custom telemetry and dependency tracking
- Azure Monitor workbooks for custom dashboards

### Governance

- Unity Catalog catalog + lineage
- Data contracts + versioning policy (see [Data Governance](../data/governance.md))
- Retention policies per zone (Bronze/Silver/Gold)
- Workspace-level access controls and audit logs

