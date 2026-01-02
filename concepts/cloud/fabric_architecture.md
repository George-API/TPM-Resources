# Fabric Data Architecture Patterns

**Scope**: Microsoft Fabric-centric enterprise data platform implementation (ingest → lakehouse → serving → BI).

## Table of Contents

- [Core Stack](#core-stack)
- [1. Reference Architecture (Medallion Lakehouse)](#1-reference-architecture-medallion-lakehouse)
- [2. Microsoft Fabric Patterns](#2-microsoft-fabric-patterns)
- [3. Security & Governance](#3-security--governance)
- [4. Reliability & Data Freshness](#4-reliability--data-freshness)
- [5. Data Modeling & Serving](#5-data-modeling--serving)
- [6. Cost & Performance](#6-cost--performance)
- [7. Default Controls Checklist](#7-default-controls-checklist)

---

## Core Stack

**Typical Fabric stack:**

- **Ingestion/Orchestration**: Data Factory (pipelines, data flows)
- **Streaming**: Real-Time Intelligence / Real-Time Hub (Event Hubs, IoT Hub, streaming sources)
- **Storage/Lake**: OneLake (unified storage), Delta/Parquet
- **Processing**: Data Engineering (Lakehouse, Spark, Notebooks)
- **Serving**: Data Warehouse (SQL analytics), Databases (operational SQL)
- **Semantic/BI**: Power BI (native integration, Direct Lake)
- **AI/ML**: Copilot (AI assistance), Data Science (ML workloads)
- **Governance**: Microsoft Purview (built-in)
- **Security/Monitoring**: Entra ID, Key Vault, Defender for Cloud, Fabric monitoring

---

## 1. Reference Architecture (Medallion Lakehouse)

### A) Sources

- SaaS (Dynamics, ServiceNow, etc.), on-prem DBs, files, APIs, IoT/telemetry

### B) Ingestion Layer

- **Batch**: Data Factory pipelines (copy + transforms) → Bronze
- **CDC**: source CDC → landing → Bronze (or event stream)
- **Streaming**: Real-Time Intelligence / Real-Time Hub → stream processing → Bronze/Silver

### C) Lakehouse Layers (Medallion)

- **Bronze**: raw/append-only, immutable, source-aligned partitions
- **Silver**: cleaned/standardized, deduped, schema-enforced, conformed dimensions
- **Gold**: curated business entities / aggregates for analytics + BI serving

### D) Serving

- **Warehouse/SQL**: Data Warehouse for BI-friendly consumption
- **APIs**: lightweight serving via App Service/Functions if operational consumers need data products

### E) Governance/Operations

- Purview for catalog + lineage + classification
- Fabric monitoring for pipeline/run telemetry, freshness, failures
- Data quality gates + quarantine + replay tooling

---

## 2. Microsoft Fabric Patterns

### Real-Time Intelligence

- **Real-Time Hub**: unified SaaS experience for data-in-motion
- **Streaming sources**: Event Hubs, IoT Hub, Microsoft 365 events, external streams
- **Stream processing**: real-time transformation and analytics
- **Event routing**: route events to destinations (OneLake, Event Hubs, etc.)
- **Low-latency analytics**: query streaming data with minimal delay

### OneLake (Unified Storage)

- **Single logical lake**: tenant-wide unified storage; no data movement between workspaces
- **Shortcuts**: reference external data (ADLS, S3, Google Cloud) without copying
- **Delta format**: default for lakehouse tables; ACID transactions, time travel
- **Partitioning**: by date/tenant/source; optimize file sizes (128MB-1GB target)
- **Workspace isolation**: each workspace has its own OneLake location
- **Zero-copy access**: data shared across workloads without duplication

### Medallion Architecture Implementation

- **Bronze (Raw)**: 
  - OneLake location: `lakehouse/bronze/{source}/{table}/`
  - Append-only; immutable; source-aligned partitions
  - Schema-on-read; minimal validation
- **Silver (Cleaned)**:
  - OneLake location: `lakehouse/silver/{domain}/{table}/`
  - Deduplicated; standardized; schema-enforced
  - Quality checks; quarantine bad records
- **Gold (Curated)**:
  - OneLake location: `lakehouse/gold/{domain}/{table}/`
  - Business entities; star schemas; aggregates
  - Certified datasets; documented semantics

### Data Engineering (Lakehouse)

- **Spark engine**: built-in Spark runtime for transformations
- **SQL endpoint**: automatic SQL endpoint for querying lakehouse tables
- **Notebooks**: Python/Scala/R notebooks for data engineering
- **Shortcuts**: connect to external storage without data duplication
- **Copilot**: AI-assisted code generation, query optimization, documentation

### Data Warehouse

- **SQL analytics**: fully managed SQL data warehouse
- **Direct lake access**: query OneLake data directly without import
- **Automatic indexing**: query optimization built-in
- **T-SQL compatibility**: standard SQL Server syntax
- **Copilot**: AI-assisted SQL query generation and optimization

### Data Factory

- **Pipelines**: orchestration and data movement (copy activities)
- **Data flows**: Spark-based transformations (mapping data flows)
- **Workspace integration**: native integration with lakehouse and warehouse
- **Triggers**: schedule-based, event-based
- **Copilot**: AI-assisted pipeline authoring and code generation

### Semantic Models (Power BI)

- **Import mode**: data copied into Power BI (fast, limited size)
- **DirectQuery**: live queries to Fabric Warehouse (real-time, larger datasets)
- **Direct Lake**: query OneLake Delta tables directly (no import, no DirectQuery overhead; best performance)
- **Composite models**: mix import + DirectQuery for optimal performance
- **Certified datasets**: governance-approved; promoted in Power BI service
- **Copilot**: AI-assisted report creation, DAX generation, insights

### Data Networking (Fabric)

- **ADLS Private Link**: Private endpoints for OneLake/ADLS Gen2 access
- **Fabric Private Link**: Private endpoints for Fabric workspace access (check current GA status)
- **Data plane isolation**: separate subnets for data ingestion vs serving
- **Egress control**: NAT Gateway for stable outbound IPs (source systems allowlists)

> **Note**: For general networking patterns, see [Cloud Architecture](architecture.md). For OSI troubleshooting, see [OSI Model](../software/osi.md).

---

## 3. Security & Governance

### Identity

- Entra ID everywhere; Managed Identity for Fabric Pipelines/Functions where available
- PIM for privileged roles; workspace-level RBAC

### Data access controls

- **Storage**: RBAC on OneLake; workspace-level and item-level permissions
- **Serving**: RLS (Power BI/SQL) for business-driven partitioning; CLS for sensitive columns
- **Keys/secrets**: Key Vault (CMK when required); rotate certificates

### Governance

- **Purview (built-in)**:
  - catalog + lineage for pipelines and lakehouse assets
  - classification labels for sensitive fields
  - data contracts enforcement (schema validation)
  - centralized data discovery and access control
- **Fabric governance**:
  - workspace-level access control, item-level permissions
  - data sensitivity labels, retention policies
  - workspace settings and policies
  - admin portal for tenant-wide governance

> **Note**: For data management concepts (operating model, quality, integrity), see [Data Governance](../data/governance.md), [Data Operations](../data/operations.md), and [Data Security](../data/security.md).

---

## 4. Reliability & Data Freshness

> **Note**: For data SLO concepts (what they are, why they matter), see [Data Operations - Reliability](../data/operations.md#8-reliability--data-sre).

### Implementing Data SLOs (Fabric)

- **Freshness SLO**: Monitor Fabric pipeline completion times; alert on SLA breaches
  - Use Fabric pipeline run metrics + Log Analytics queries
  - Alert when Gold partition update exceeds SLA window
- **Completeness SLO**: Compare record counts vs source control totals
  - Use data quality checks in Fabric pipelines; publish metrics
- **Accuracy SLO**: Quality check pass rates
  - Integrate Great Expectations or custom checks; publish to dashboard
- **Availability SLO**: Power BI semantic model uptime; Fabric Warehouse query success rates

### Operational patterns

- **Watermarking**:
  - track high-water mark per source/table; store in control table (Fabric Warehouse)
- **Idempotency**:
  - reruns should not duplicate (merge/upsert patterns in Fabric SQL)
- **Backpressure**:
  - autoscale consumers on Event Hubs/queues; throttle upstream if downstream is hot
- **Quarantine**:
  - bad rows → quarantine storage (separate lakehouse/table) + defect metrics + triage workflow

---

## 5. Data Modeling & Serving

### Gold Layer Design

- **Star schemas**: fact tables + dimension tables for BI (Power BI optimized)
- **Curated wide tables**: denormalized where justified (fewer joins, faster queries)
- **Partitioning**: by date for time-series; by tenant for multi-tenant
- **Indexing**: Fabric Warehouse automatic indexing; manual optimization for large tables

### Data Products

- **Fabric data products**: publish Gold tables as certified datasets
- **Contracts**: schema, semantics, keys, SLAs defined in Purview
- **Documentation**: Power BI semantic model descriptions, sample queries, changelog

> **Note**: For data contract concepts and operating model, see [Data Governance](../data/governance.md).

---

## 6. Cost & Performance

- **Partitioning strategy**:
  - by date (and sometimes tenant/source) for lakehouse
- **File sizing**:
  - avoid many tiny files; compact/optimize Silver/Gold regularly
- **Avoid moving data unnecessarily**:
  - pushdown filters, incremental loads, CDC where possible
  - use shortcuts to reference external data
- **Choose the right Fabric workload**:
  - Data Factory for orchestration + movement
  - Data Engineering (Lakehouse/Spark) for heavy transforms
  - Data Warehouse for serving + BI transforms
  - Real-Time Intelligence for streaming scenarios
- **Direct Lake mode**:
  - Power BI Direct Lake queries OneLake directly (no import, no DirectQuery overhead)

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

- Pipeline run telemetry + alerts (Fabric monitoring)
- Freshness + backlog dashboards
- Cost per pipeline + anomaly alerts
- Capacity metrics and utilization tracking

### Governance

- Purview catalog + lineage
- Data contracts + versioning policy (see [Data Governance](../data/governance.md))
- Retention policies per zone (Bronze/Silver/Gold)
- Fabric workspace governance settings

