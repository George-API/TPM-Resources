# Azure Data Platform Implementation

**Scope**: Azure-centric enterprise data platform implementation (ingest → lake → serving → BI).

## Table of Contents

- [Core Stack](#core-stack)
- [1. Reference Architecture (Medallion Lakehouse)](#1-reference-architecture-medallion-lakehouse)
- [2. Azure Data Platform Patterns](#2-azure-data-platform-patterns)
- [3. Security & Governance](#3-security--governance)
- [4. Reliability & Data Freshness](#4-reliability--data-freshness)
- [5. Data Modeling & Serving](#5-data-modeling--serving)
- [6. Cost & Performance](#6-cost--performance)
- [7. Default Controls Checklist](#7-default-controls-checklist)

---

## Core Stack

**Typical Azure stack:**

- **Ingestion/Orchestration**: Azure Data Factory (ADF)
- **Streaming**: Event Hubs (telemetry/streams), Event Grid (events), Service Bus (commands/queues)
- **Storage/Lake**: Azure Data Lake Storage Gen2 (ADLS Gen2), Delta/Parquet
- **Processing**: Azure Synapse Analytics / Databricks / HDInsight
- **Serving**: Azure SQL Database / Azure SQL Managed Instance / Synapse SQL
- **Semantic/BI**: Power BI
- **Governance**: Microsoft Purview
- **Security/Monitoring**: Entra ID, Key Vault, Defender for Cloud, Azure Monitor/App Insights

---

## 1. Reference Architecture (Medallion Lakehouse)

### A) Sources

- SaaS (Dynamics, ServiceNow, etc.), on-prem DBs, files, APIs, IoT/telemetry

### B) Ingestion Layer

- **Batch**: Azure Data Factory (copy + transforms) → Bronze
- **CDC**: source CDC → landing → Bronze (or event stream)
- **Streaming**: Event Hubs → stream processing (Stream Analytics/Synapse) → Bronze/Silver

### C) Lakehouse Layers (Medallion)

- **Bronze**: raw/append-only, immutable, source-aligned partitions
- **Silver**: cleaned/standardized, deduped, schema-enforced, conformed dimensions
- **Gold**: curated business entities / aggregates for analytics + BI serving

### D) Serving

- **Warehouse/SQL**: Synapse SQL / Azure SQL for BI-friendly consumption
- **APIs**: lightweight serving via App Service/Functions if operational consumers need data products

### E) Governance/Operations

- Purview for catalog + lineage + classification
- Monitor + Log Analytics for pipeline/run telemetry, freshness, failures
- Data quality gates + quarantine + replay tooling

---

## 2. Azure Data Platform Patterns

### ADLS Gen2 (Storage)

- **Hierarchical namespace**: enables directory semantics and POSIX-like access
- **Delta format**: ACID transactions, time travel, schema evolution
- **Partitioning**: by date/tenant/source; optimize file sizes (128MB-1GB target)
- **Access control**: RBAC + ACLs for fine-grained permissions
- **Lifecycle management**: hot/cool/archive tiers, automatic tiering

### Medallion Architecture Implementation

- **Bronze (Raw)**: 
  - ADLS location: `{container}/bronze/{source}/{table}/`
  - Append-only; immutable; source-aligned partitions
  - Schema-on-read; minimal validation
- **Silver (Cleaned)**:
  - ADLS location: `{container}/silver/{domain}/{table}/`
  - Deduplicated; standardized; schema-enforced
  - Quality checks; quarantine bad records
- **Gold (Curated)**:
  - ADLS location: `{container}/gold/{domain}/{table}/`
  - Business entities; star schemas; aggregates
  - Certified datasets; documented semantics

### Azure Data Factory Patterns

- **Copy activities**: efficient data movement between sources and ADLS
- **Data flows**: Spark-based transformations (mapping data flows)
- **Linked services**: connection management with Managed Identity
- **Triggers**: schedule-based, event-based (Event Grid), tumbling window
- **Integration runtime**: Azure IR, self-hosted IR for on-prem sources

### Synapse Analytics

- **Synapse SQL (dedicated)**: provisioned SQL pools for data warehousing
- **Synapse SQL (serverless)**: serverless SQL for ad-hoc queries on lake
- **Synapse Spark**: Spark pools for data engineering and ML
- **Workspace integration**: unified experience for SQL, Spark, and pipelines

### Data Networking (Azure)

- **Private endpoints**: Private endpoints for ADLS Gen2, Synapse, SQL
- **Private Link**: secure connectivity without public internet
- **VNet integration**: Synapse managed VNet, ADF VNet integration
- **Data plane isolation**: separate subnets for data ingestion vs serving
- **Egress control**: NAT Gateway for stable outbound IPs (source systems allowlists)

> **Note**: For general networking patterns, see [Cloud Architecture](architecture.md). For OSI troubleshooting, see [OSI Model](../software/osi.md).

---

## 3. Security & Governance

### Identity

- Entra ID everywhere; Managed Identity for ADF/Synapse/Functions where available
- PIM for privileged roles; scoped RBAC per subscription/resource group

### Data access controls

- **Storage**: RBAC + ACLs (ADLS Gen2) carefully designed (least privilege)
- **Serving**: RLS (SQL) for business-driven partitioning; CLS for sensitive columns
- **Keys/secrets**: Key Vault (CMK when required); rotate certificates

### Governance

- **Purview**:
  - catalog + lineage for pipelines and lakehouse assets
  - classification labels for sensitive fields
  - data contracts enforcement (schema validation)
- **Azure Policy**:
  - deny public endpoints on data stores, enforce private link, enforce tagging, restrict regions/SKUs
- **Resource locks**: prevent accidental deletion of critical resources

> **Note**: For data management concepts (operating model, quality, integrity), see [Data Management](../data/management.md).

---

## 4. Reliability & Data Freshness

> **Note**: For data SLO concepts (what they are, why they matter), see [Data Management - Reliability](../data/management.md#8-reliability-resilience-and-data-sre-advanced).

### Implementing Data SLOs (Azure)

- **Freshness SLO**: Monitor ADF pipeline completion times; alert on SLA breaches
  - Use Log Analytics queries on ADF run metrics
  - Alert when Gold partition update exceeds SLA window
- **Completeness SLO**: Compare record counts vs source control totals
  - Use data quality checks in ADF pipelines; publish metrics to Log Analytics
- **Accuracy SLO**: Quality check pass rates
  - Integrate Great Expectations or custom checks; publish to dashboard
- **Availability SLO**: SQL database uptime; Synapse query success rates

### Operational patterns

- **Watermarking**:
  - track high-water mark per source/table; store in control table (Azure SQL)
- **Idempotency**:
  - reruns should not duplicate (merge/upsert patterns in Synapse SQL)
- **Backpressure**:
  - autoscale consumers on Event Hubs/queues; throttle upstream if downstream is hot
- **Quarantine**:
  - bad rows → quarantine storage (separate ADLS container) + defect metrics + triage workflow

---

## 5. Data Modeling & Serving

### Gold Layer Design

- **Star schemas**: fact tables + dimension tables for BI (Power BI optimized)
- **Curated wide tables**: denormalized where justified (fewer joins, faster queries)
- **Partitioning**: by date for time-series; by tenant for multi-tenant
- **Indexing**: Synapse SQL automatic indexing; manual optimization for large tables

### Data Products

- **Azure data products**: publish Gold tables as certified datasets in Purview
- **Contracts**: schema, semantics, keys, SLAs defined in Purview
- **Documentation**: Power BI semantic model descriptions, sample queries, changelog

> **Note**: For data contract concepts and operating model, see [Data Management](../data/management.md).

---

## 6. Cost & Performance

- **Partitioning strategy**:
  - by date (and sometimes tenant/source) for lake
- **File sizing**:
  - avoid many tiny files; compact/optimize Silver/Gold regularly
- **Avoid moving data unnecessarily**:
  - pushdown filters, incremental loads, CDC where possible
- **Choose the right integration tool**:
  - ADF for orchestration + movement
  - Synapse Spark for heavy transforms
  - Synapse SQL for serving + BI transforms
- **Synapse SQL optimization**:
  - table distribution (hash, round-robin, replicated)
  - columnstore indexes for analytics workloads
  - result set caching for repeated queries

---

## 7. "Default Controls" Checklist

### Network

- Private endpoints + Private DNS Zones
- Deny public network access for data stores
- Central egress via Firewall/NAT

### Identity

- Managed Identity everywhere possible
- Least privilege RBAC + PIM for admins

### Data hygiene

- Bronze append-only; Silver validated; Gold curated
- Schema validation + quarantine
- Watermarks + idempotent reruns

### Observability

- Pipeline run telemetry + alerts (Log Analytics)
- Freshness + backlog dashboards
- Cost per pipeline + anomaly alerts

### Governance

- Purview catalog + lineage
- Data contracts + versioning policy (see [Data Management](../data/management.md))
- Retention policies per zone (Bronze/Silver/Gold)

