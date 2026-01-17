# Azure Architecture Patterns

**Scope**: Azure-centric enterprise architecture patterns covering data platforms (ingest → lake → serving → BI) and general Azure compute, networking, and hosting services.

**Purpose**: Use this for Azure architecture design and implementation. For Microsoft Fabric patterns, see [Fabric Architecture](fabric_architecture.md). For Databricks patterns, see [Databricks Architecture](databricks_architecture.md). For optimization practices, see [Cloud Optimization](optimization.md).

## Table of Contents

- [Core Stack](#core-stack)
- [1. Reference Architecture (Medallion Lakehouse)](#1-reference-architecture-medallion-lakehouse)
- [2. Azure Data Platform Patterns](#2-azure-data-platform-patterns)
- [3. Enterprise Resource Organization](#3-enterprise-resource-organization)
- [4. Azure Compute & Hosting](#4-azure-compute--hosting)
- [5. Security & Governance](#5-security--governance)
- [6. Reliability & Data Freshness](#6-reliability--data-freshness)
- [7. Data Modeling & Serving](#7-data-modeling--serving)
- [8. Cost & Performance](#8-cost--performance)
- [9. Default Controls Checklist](#9-default-controls-checklist)

---

## Core Stack

**Typical Azure data stack:**

- **Ingestion/Orchestration**: Azure Data Factory (ADF)
- **Streaming**: Event Hubs (telemetry/streams), Event Grid (events), Service Bus (commands/queues), Stream Analytics (real-time processing)
- **Storage/Lake**: Azure Data Lake Storage Gen2 (ADLS Gen2), Delta/Parquet
- **Processing**: Azure Synapse Analytics / Databricks / HDInsight
- **Serving**: Azure SQL Database / Azure SQL Managed Instance / Synapse SQL
- **Semantic/BI**: Power BI
- **Governance**: Microsoft Purview
- **Security/Monitoring**: Entra ID, Key Vault, Defender for Cloud, Azure Monitor/App Insights

**Typical Azure application stack:**

- **Compute**: App Service (web/API), Azure Functions (serverless), AKS (containers), Virtual Machines (IaaS)
- **Networking**: Application Gateway, Front Door, Load Balancer, Virtual Network
- **Integration**: API Management, Logic Apps, Service Bus, Event Grid
- **Storage**: Blob Storage, Azure Files, Azure Tables, Cosmos DB
- **Security/Monitoring**: Entra ID, Key Vault, Defender for Cloud, Application Insights

---

## 1. Reference Architecture (Medallion Lakehouse)

### A) Sources

- SaaS (Dynamics, ServiceNow, etc.), on-prem DBs, files, APIs, IoT/telemetry

### B) Ingestion Layer

- **Batch**: Azure Data Factory (copy + transforms) → Bronze
- **CDC**: source CDC → landing → Bronze (or event stream)
- **Streaming**: Event Hubs → stream processing (Stream Analytics/Synapse Spark streaming) → Bronze/Silver

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
- **Immutable storage**: WORM (Write Once, Read Many) for compliance scenarios
- **Soft delete**: recover deleted blobs within retention period

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

- **Copy activities**: Efficient data movement, parallel copy, compression
- **Data flows**: Spark-based transformations (mapping data flows)
- **Linked services**: Managed Identity connections, parameterized connections
- **Triggers**: Schedule-based, event-based (Event Grid), tumbling window
- **Integration runtime**: Azure IR (cloud), self-hosted IR (on-prem), Azure-SSIS IR
- **Best practices**: Git integration (version control, CI/CD), private endpoints, error handling (try-catch, retry policies), Log Analytics monitoring

### Synapse Analytics

- **Synapse SQL (dedicated)**: Provisioned SQL pools, pause/resume for cost optimization
- **Synapse SQL (serverless)**: Serverless SQL for ad-hoc queries on lake (pay per query)
- **Synapse Spark**: Spark pools for data engineering and ML, auto-scale, auto-pause
- **Lake database**: Unified metadata layer across lake and warehouse
- **Security**: Row-level security, column-level security, Always Encrypted
- **Best practices**: Git integration, Managed Identity, private endpoints, workspace-level RBAC

### Data Networking (Azure)

- **Private endpoints**: Use for all PaaS services (ADLS, Synapse, SQL, ADF, Key Vault); Private DNS Zones for resolution
- **Disable public access**: Deny public network access on all data stores by default
- **VNet integration**: Synapse managed VNet, ADF VNet integration, App Service VNet integration
- **Network segmentation**: Separate subnets for ingestion vs serving; NSGs (deny by default); Azure Firewall for centralized security
- **Egress control**: NAT Gateway for stable outbound IPs; monitor outbound traffic for anomalies

> **Note**: For general networking patterns, see [Cloud Architecture](architecture.md). For OSI troubleshooting, see [OSI Model](../software/osi.md).

---

## 3. Enterprise Resource Organization

### Management Groups & Subscriptions

- **Management groups**: Organize subscriptions hierarchically (root → department → environment); apply policies at scale
- **Subscription strategy**: Separate subscriptions for dev/test/prod, workloads, compliance requirements
- **Resource groups**: Group by application/environment/lifecycle; consistent naming; RBAC at resource group level

### Tagging Strategy

- **Tag categories**: Environment, cost center, owner, compliance, application
- **Tag governance**: Azure Policy to enforce required tags; standardize tag values; use for cost allocation

### Cost Management

- **Budget & forecasting**: Budget alerts per subscription/resource group; Azure Cost Management for forecasting
- **Reserved Instances/Savings Plans**: Use for predictable workloads (1-3 year commitments)
- **Cost optimization**: Right-size resources; auto-scale down during off-peak; automate resource cleanup; review Azure Advisor recommendations

---

## 4. Azure Compute & Hosting

### Azure App Service

- **Platform**: Fully managed PaaS for web applications, APIs, mobile backends (.NET, Node.js, Python, Java, etc.)
- **Features**: Auto-scaling, staging slots, Managed Identity, custom domains, SSL/TLS
- **Best practices**: Staging slots for blue-green deployments, auto-scaling, health checks, Application Insights

### Azure Functions (Serverless)

- **Triggers**: HTTP, Timer, Queue, Blob, Event Grid, Event Hubs, Cosmos DB
- **Hosting plans**: Consumption (pay-per-execution), Premium (always warm), Dedicated (App Service)
- **Durable Functions**: Stateful workflows, orchestrations, long-running processes
- **Best practices**: Managed Identity, concurrency limits, Durable Functions for complex workflows, monitor cold starts, idempotency

### Container Services

- **Azure Container Instances (ACI)**: Simple container workloads, batch jobs, dev/test (fast startup, per-second billing)
- **Azure Kubernetes Service (AKS)**: Managed Kubernetes, auto-scaling, rolling updates, service mesh support
- **Best practices**: Managed node pools, cluster autoscaler, Azure CNI for advanced networking, pod security standards, Azure Policy, Container Insights

### Virtual Machines

- **Use cases**: Lift-and-shift, legacy applications, specialized workloads
- **Availability**: Availability Zones, Virtual Machine Scale Sets, managed disks
- **Best practices**: Availability Zones for HA, Scale Sets for auto-scaling, managed disks, Azure Backup, Azure Monitor, Azure Policy

### Networking Services

- **Application Gateway**: Layer 7 load balancer, WAF, URL-based routing (multi-tier apps, API gateways)
- **Azure Front Door**: Global load balancer, CDN, DDoS protection (global apps, multi-region)
- **Azure Load Balancer**: Layer 4 load balancer (TCP/UDP), public/internal (non-HTTP traffic, internal load balancing)
- **Best practices**: WAF enabled, health probes, Standard SKU for production, Application Insights integration

### Integration Services

- **API Management (APIM)**: Centralized API management, versioning, rate limiting, caching (App Service, Functions, Logic Apps backends)
- **Logic Apps**: Serverless workflows, 400+ connectors (enterprise integration, B2B workflows, automation)
- **Best practices**: APIM policies and caching, Logic Apps Managed Identity, Application Insights monitoring

### Messaging Services

- **Service Bus**: Enterprise messaging (queues, topics/subscriptions), at-least-once delivery, dead-letter queues, sessions (decoupled microservices, reliable messaging)
- **Event Grid**: Serverless event routing, pub/sub (event-driven architectures, reactive systems)
- **Best practices**: Service Bus dead-letter queues and message TTL; Event Grid event filters and idempotency

> **Note**: For container orchestration patterns, see [Cloud Infrastructure & Operations](infrastructure.md). For serverless patterns, see [Cloud Infrastructure & Operations - Serverless](infrastructure.md#3-serverless-patterns).

---

## 5. Security & Governance

### Identity & Access Management

- **Managed Identity**: Use for all Azure resources (no secrets); preferred over service principals
- **Conditional Access**: Enforce MFA, device compliance, location-based access, risk-based policies
- **PIM**: Just-in-time access, time-bound roles, approval workflows for privileged access
- **RBAC**: Least privilege access, role-based assignments, deny assignments
- **Zero Trust**: Verify explicitly, least privilege, assume breach; continuous validation
- **Key Vault**: Store secrets when necessary; use Managed Identity for access; automated rotation

### Network Security

- **Private endpoints**: Use for all PaaS services; Private DNS Zones for resolution; deny public access on data stores
- **Network segmentation**: Separate subnets (ingestion vs serving); NSGs (deny by default); Azure Firewall for centralized security
- **DDoS Protection**: Enable DDoS Protection Standard for production
- **Zero Trust**: Authenticate all service-to-service (Managed Identity); Conditional Access for user access; monitor network traffic

### Data Access Controls

- **Storage**: RBAC + ACLs (ADLS Gen2); encryption at rest (Azure-managed or CMK) and in transit (TLS); appropriate access tiers
- **Database**: Row-Level Security (RLS), Column-Level Security (CLS), Always Encrypted, TDE
- **Key Vault**: Centralized key/secret management, CMK for compliance, automated rotation, RBAC access

### Governance & Compliance

- **Microsoft Purview**: Data catalog, lineage, classification, data contracts, access policies
- **Azure Policy**: Enforce standards (deny public endpoints, require tagging, restrict regions/SKUs); policy initiatives; automatic remediation
- **Resource locks**: Delete/read-only locks on critical resources
- **Compliance**: ISO 27001, SOC 2, GDPR, HIPAA, NIST, FedRAMP; Azure Compliance Manager; audit logs in Log Analytics; activity logs

### Threat Protection

- **Microsoft Defender for Cloud**: Continuous security assessment, threat detection, vulnerability management, security alerts
- **Microsoft Sentinel (SIEM)**: Centralized security information, threat hunting, incident response, compliance reporting

> **Note**: For data management concepts (operating model, quality, integrity), see [Data Governance](../data/governance.md), [Data Operations](../data/operations.md), and [Data Security](../data/security.md).

---

## 6. Reliability & Data Freshness

> **Note**: For data SLO concepts (what they are, why they matter), see [Data Operations - Reliability](../data/operations.md#8-reliability--data-sre).

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

## 7. Data Modeling & Serving

### Gold Layer Design

- **Star schemas**: fact tables + dimension tables for BI (Power BI optimized)
- **Curated wide tables**: denormalized where justified (fewer joins, faster queries)
- **Partitioning**: by date for time-series; by tenant for multi-tenant
- **Indexing**: Synapse SQL automatic indexing; manual optimization for large tables

### Data Products

- **Azure data products**: publish Gold tables as certified datasets in Purview
- **Contracts**: schema, semantics, keys, SLAs defined in Purview
- **Documentation**: Power BI semantic model descriptions, sample queries, changelog

> **Note**: For data contract concepts and operating model, see [Data Governance](../data/governance.md).

---

## 8. Cost & Performance

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
  - materialized views for pre-aggregated data
  - workload management: resource classes and workload groups for query prioritization

### Disaster Recovery & High Availability

- **Storage**: ADLS Gen2 geo-redundant storage (GRS/RA-GRS), soft delete, immutable storage (WORM)
- **Compute**: Synapse SQL automated backups (7-35 days), geo-restore; Azure SQL geo-replication, failover groups; App Service multi-region; AKS multi-region clusters
- **Pipelines**: ADF in Git, parameterized for DR; Event Hubs/Service Bus geo-disaster recovery
- **Recovery planning**: Define RTO/RPO per workload tier; test DR regularly; validate backups; monitor DR readiness

### CI/CD for Data Pipelines

- **Source control**: Git integration (ADF, Synapse); Infrastructure as Code (ARM, Bicep, Terraform)
- **Deployment**: Azure DevOps/GitHub Actions; environment promotion (dev → test → prod); approval gates
- **Testing**: Unit tests (transformations, SQL), integration tests (pipeline runs), data quality tests, performance tests
- **Best practices**: Key Vault for secrets, Managed Identity, infrastructure validation, rollback strategy, deployment monitoring

### Monitoring & Alerting

- **Pipeline monitoring**: ADF run metrics in Log Analytics; custom metrics via Application Insights; data freshness alerts; backlog monitoring
- **Cost management**: Budget alerts, cost analysis, Azure Advisor recommendations, FinOps practices
- **Performance monitoring**: Query performance insights (Synapse SQL), Spark metrics, Application Insights, database performance insights
- **Dashboards**: Azure Monitor workbooks, Power BI dashboards, Grafana, Azure Service Health
- **Alerting**: Configure alerts for critical metrics; group alerts; multiple channels (Teams, PagerDuty, ITSM); runbook automation

### Error Handling & Retry Strategies

- **ADF retry policies**: Retry count and interval per activity; exponential backoff for transient failures
- **Dead letter queues**: Event Hubs/Service Bus dead-letter queues for failed messages
- **Pipeline error handling**: Try-catch blocks, conditional activities for error paths
- **Idempotency**: Merge/upsert patterns; watermark-based incremental loads prevent duplicates
- **Notification**: Email/Slack/ITSM alerts on pipeline failures

### Data Lifecycle & Archiving

- **ADLS lifecycle policies**: automatic tiering (hot → cool → archive); delete old partitions after retention period
- **Bronze retention**: retain raw data per compliance requirements (typically 7-90 days)
- **Silver retention**: retain cleaned data longer (typically 1-3 years)
- **Gold retention**: retain curated data indefinitely or per business requirements
- **Archive strategy**: move cold data to Archive tier; restore from archive when needed (hours to days)

---

## 9. "Default Controls" Checklist

### Network Security

- [ ] Private endpoints for all PaaS services + Private DNS Zones
- [ ] Deny public network access for all data stores
- [ ] Central egress via Firewall/NAT Gateway
- [ ] Network Security Groups (NSGs) with deny-by-default rules
- [ ] Azure Firewall for centralized network security
- [ ] DDoS Protection Standard enabled for production

### Identity & Access

- [ ] Managed Identity everywhere possible (no connection strings)
- [ ] Least privilege RBAC + PIM for admins
- [ ] Conditional Access policies (MFA, device compliance, risk-based)
- [ ] Regular access reviews, remove unused access
- [ ] Key Vault for secrets, automated rotation
- [ ] Zero Trust principles: verify explicitly, least privilege, assume breach

### Data Security

- [ ] Encryption at rest (Azure-managed or customer-managed keys)
- [ ] Encryption in transit (TLS 1.2+)
- [ ] Row-Level Security (RLS) and Column-Level Security (CLS) where applicable
- [ ] Data classification and sensitivity labels (Purview)
- [ ] Access logging and audit trails enabled
- [ ] Customer-Managed Keys (CMK) for compliance requirements

### Data Hygiene

- [ ] Bronze append-only; Silver validated; Gold curated
- [ ] Schema validation + quarantine bad records
- [ ] Watermarks + idempotent reruns
- [ ] Data quality checks in pipelines
- [ ] Retention policies per zone (Bronze/Silver/Gold)

### Observability

- [ ] Pipeline run telemetry + alerts (Log Analytics)
- [ ] Freshness + backlog dashboards
- [ ] Cost per pipeline + anomaly alerts
- [ ] Application Insights for custom telemetry and dependency tracking
- [ ] Azure Monitor workbooks for custom dashboards
- [ ] Security alerts (Defender for Cloud, Sentinel)

### Governance

- [ ] Purview catalog + lineage
- [ ] Data contracts + versioning policy (see [Data Governance](../data/governance.md))
- [ ] Azure Policy initiatives applied (compliance, security, cost)
- [ ] Resource tagging strategy (cost allocation, compliance)
- [ ] Management groups for subscription organization
- [ ] Resource locks on critical production resources
- [ ] Compliance monitoring and reporting

### Backup & Disaster Recovery

- [ ] Automated backups configured (appropriate retention)
- [ ] Cross-region replication for critical data
- [ ] DR runbooks documented and tested
- [ ] RTO/RPO targets defined per workload tier
- [ ] Backup validation and restore testing

