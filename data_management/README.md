# Data Management

**Purpose**: Data concepts, data-platform cloud (Fabric, Databricks, Azure), and alignment to DMBOK and related standards.

---

## Concepts

**[concepts/](concepts/)** — **Core** (governance, operations, DataOps, engineering, database, data stores, security, analytics, analytics engineering) and **platform** (architecture, Azure, Databricks, Fabric, infrastructure, optimization, streaming, Power BI). See [concepts/core/](concepts/core/) and [concepts/platform/](concepts/platform/).

---

## Data Management in Practice

- **Data management** covers the full lifecycle: strategy, governance, architecture, quality, security, and operations so that data is fit for use and aligned to business goals.
- **DMBOK (DAMA-DMBOK)** defines knowledge areas that map to the concept docs below.

---

## DMBOK Knowledge Areas → Concept Docs

| DMBOK area | Focus | Use this doc |
|------------|--------|---------------|
| Data Governance | Operating model, ownership, policy, stewardship | [Data Governance](concepts/core/governance.md) |
| Data Architecture | Data structures, integration, storage, flows | [Data Engineering](concepts/core/engineering.md), [Database](concepts/core/database.md), [Data Stores](concepts/core/data_stores.md), platform docs |
| Data Modeling & Design | Models, schemas, normalization | [Database](concepts/core/database.md), [Data Engineering](concepts/core/engineering.md) |
| Data Storage & Operations | RDBMS, NoSQL, object storage, backup, HA | [Database](concepts/core/database.md), [Data Stores](concepts/core/data_stores.md) |
| Data Security | Access, privacy, protection | [Data Security](concepts/core/security.md) |
| Data Integration & Interoperability | ETL/ELT, pipelines, APIs | [Data Engineering](concepts/core/engineering.md), [Streaming](concepts/platform/streaming.md) |
| Reference & Master Data | MDM, golden record | [Data Governance](concepts/core/governance.md), [Data Operations](concepts/core/operations.md) |
| Data Warehousing & BI | Warehouses, analytics, reporting | [Analytics Engineering](concepts/core/analytics_eng.md), [Analytics](concepts/core/analytics.md), [Power BI](concepts/platform/powerbi.md) |
| Metadata | Catalog, lineage, discovery | [Data Governance](concepts/core/governance.md) |
| Data Quality | Dimensions, rules, monitoring | [Data Operations](concepts/core/operations.md) |
| Data Lifecycle | Retention, archival, disposal | [Data Operations](concepts/core/operations.md) |

---

## When to Use Which Doc

| Need | Primary doc |
|------|-------------|
| Who owns data, policies, catalog, lineage | [Data Governance](concepts/core/governance.md) |
| DataOps methodology, culture, automation, frameworks | [DataOps](concepts/core/dataops.md) |
| Quality, integrity, lifecycle, reliability, SRE | [Data Operations](concepts/core/operations.md) |
| Pipelines, ETL/ELT, orchestration, modeling | [Data Engineering](concepts/core/engineering.md) |
| RDBMS design, indexing, transactions, backup, HA | [Database](concepts/core/database.md) |
| OLTP/OLAP, NoSQL, object storage, store selection | [Data Stores](concepts/core/data_stores.md) |
| Security, access, privacy, encryption | [Data Security](concepts/core/security.md) |
| Streaming, real-time pipelines | [Streaming](concepts/platform/streaming.md) |
| Analytics methods, BI, semantic layer | [Analytics](concepts/core/analytics.md), [Analytics Engineering](concepts/core/analytics_eng.md), [Power BI](concepts/platform/powerbi.md) |
| Cloud/data platform (Fabric, Databricks, Azure) | [Architecture](concepts/platform/architecture.md), [Fabric](concepts/platform/fabric.md), [Databricks](concepts/platform/databricks.md), [Azure](concepts/platform/azure.md) |
| Cloud performance, cost, DX optimization | [Cloud Optimization](concepts/platform/optimization.md) |
| Infrastructure deploy, containers, CI/CD, cloud ops | [Cloud Infrastructure & Operations](concepts/platform/infrastructure.md) |

---

## Resources & Standards

- **[resources.md](resources.md)** — Standards, certifications, legislation, and tools (References + Tools combined).
- **DMBOK**: [DAMA-DMBOK](https://www.dama.org/cpages/body-of-knowledge) · **DCAM**: [EDM Council](https://edmcouncil.org/page/dcam) · **CDMP**: [cdmp.info](https://www.cdmp.info/)

---

> **Related**: [Terminology](terminology.md) · [Resources](resources.md) · [TPM](../tpm/) · [Programming](../programming/)
