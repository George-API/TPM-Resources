# Terminology Reference

**Purpose**: Quick reference for key terminology across cloud, data, software, and project management concepts.

**Related Documentation**: See [README.md](README.md) for full documentation structure.

---

## Cloud Concepts

### Architecture & Patterns

- **API Gateway**: Centralized entry point for API requests; handles routing, authentication, rate limiting
- **B2B/B2C**: Business-to-Business / Business-to-Consumer identity patterns
- **CDN (Content Delivery Network)**: Distributed network for delivering content with low latency
- **Circuit Breaker**: Pattern to prevent cascading failures by stopping requests to failing services
- **DDoS Protection**: Distributed Denial of Service protection at network edge
- **Event-Driven Architecture**: Architecture pattern using events for communication between services
- **Managed Identity**: Cloud-native identity for resources (no secrets required)
- **mTLS (Mutual TLS)**: Mutual authentication using TLS certificates
- **Multi-tenant SaaS**: Software architecture serving multiple customers from single instance
- **OIDC (OpenID Connect)**: Authentication protocol built on OAuth 2.0
- **Private Link**: Secure connectivity to cloud services without public internet
- **Service Mesh**: Infrastructure layer for managing service-to-service communication
- **SLO (Service Level Objective)**: Target level of service reliability/performance
- **WAF (Web Application Firewall)**: Firewall protecting web applications from attacks
- **Zero Trust**: Security model requiring verification for every access request

### Infrastructure & Operations

- **CI/CD**: Continuous Integration / Continuous Deployment
- **Container Orchestration**: Managing containerized applications (Kubernetes, etc.)
- **GitOps**: Operational model using Git as source of truth for infrastructure
- **IaC (Infrastructure as Code)**: Managing infrastructure through code/configuration
- **Kubernetes**: Container orchestration platform
- **Serverless**: Cloud computing model where cloud provider manages infrastructure
- **VNet (Virtual Network)**: Isolated network environment in cloud

### Azure Data Platform

- **ADF (Azure Data Factory)**: Cloud-based data integration service
- **ADLS Gen2 (Azure Data Lake Storage Gen2)**: Scalable data lake storage
- **Delta Lake**: Open-source storage layer with ACID transactions
- **Medallion Architecture**: Bronze (raw) → Silver (cleaned) → Gold (curated) data layers
- **Synapse Analytics**: Analytics service combining data warehousing and big data analytics
- **Parquet**: Columnar storage format for analytics workloads

### Microsoft Fabric

- **Direct Lake**: Power BI mode querying OneLake Delta tables directly
- **OneLake**: Unified data lake storage across Fabric workspaces
- **Real-Time Intelligence**: Fabric workload for streaming data processing
- **Shortcuts**: References to external data without copying

### Azure Databricks

- **Delta Live Tables (DLT)**: Declarative framework for building data pipelines
- **Photon Engine**: Accelerated SQL engine for faster query performance
- **Structured Streaming**: Spark API for stream processing
- **Unity Catalog**: Unified governance layer for data assets
- **Workspace**: Databricks environment for notebooks, jobs, clusters

---

## Data Concepts

### Data Engineering

- **Batch Processing**: Processing data in scheduled batches
- **CDC (Change Data Capture)**: Capturing changes from source systems
- **ELT (Extract, Load, Transform)**: Load raw data first, transform later
- **ETL (Extract, Transform, Load)**: Transform data before loading to destination
- **Idempotency**: Operation that produces same result regardless of execution count
- **Incremental Processing**: Processing only new/changed data
- **Lambda Architecture**: Architecture combining batch and streaming processing
- **Orchestration**: Coordinating multiple data pipeline tasks
- **Streaming Processing**: Real-time continuous data processing
- **Watermarking**: Tracking high-water mark for incremental data loads

### Data Architecture

- **Data Lake**: Centralized repository storing raw data in native format
- **Data Lakehouse**: Architecture combining data lake and data warehouse benefits
- **Data Warehouse**: Centralized repository for structured, queryable data
- **Medallion Architecture**: Three-layer data architecture (Bronze/Silver/Gold)
- **Star Schema**: Data warehouse schema with fact tables and dimension tables

### Data Governance

- **Data Catalog**: Inventory of data assets with metadata
- **Data Contract**: Agreement defining schema, semantics, and SLAs for data products
- **Data Lineage**: Tracking data flow from source to consumption
- **Data Product**: Self-contained, reusable data asset with defined contract
- **Metadata**: Data about data (schema, lineage, quality, usage)

### Data Operations

- **Data Freshness**: Measure of how current data is (time since last update)
- **Data Quality**: Measure of data accuracy, completeness, consistency
- **Data SLO**: Service level objectives for data (freshness, completeness, accuracy)
- **Quarantine**: Isolating bad/invalid data for review and remediation

### Database Management

- **ACID**: Atomicity, Consistency, Isolation, Durability (transaction properties)
- **Index**: Database structure improving query performance
- **Partitioning**: Dividing large tables into smaller, manageable pieces
- **Query Optimization**: Improving database query performance
- **Transaction**: Unit of work that must complete entirely or not at all

### Data Security

- **Column-Level Security (CLS)**: Restricting access to specific columns
- **Data Encryption**: Converting data to unreadable format for protection
- **Row-Level Security (RLS)**: Restricting access to specific rows based on user
- **PII (Personally Identifiable Information)**: Data that can identify individuals

---

## Software Concepts

### Architecture Patterns

- **Bounded Context**: Explicit boundary where domain model applies (DDD)
- **CQRS (Command Query Responsibility Segregation)**: Separating read and write operations
- **Event Sourcing**: Storing state changes as sequence of events
- **Hexagonal Architecture**: Architecture isolating business logic from infrastructure
- **Microservices**: Architecture pattern with independently deployable services
- **Monolith**: Single deployable unit containing all application functionality

### Design Patterns

- **DDD (Domain-Driven Design)**: Software design approach focusing on domain model
- **SOLID Principles**: Five object-oriented design principles
- **Design Pattern**: Reusable solution to common software design problems

### DevOps

- **Blue-Green Deployment**: Deployment strategy with two identical environments
- **Canary Deployment**: Gradual rollout to subset of users
- **Feature Flag**: Toggle enabling/disabling features without code deployment
- **MTTR (Mean Time To Recovery)**: Average time to recover from failure

### SDLC

- **Agile**: Iterative software development methodology
- **Scrum**: Agile framework with sprints, roles, ceremonies
- **Waterfall**: Sequential software development methodology
- **Sprint**: Time-boxed iteration in Agile development

### Networking

- **OSI Model**: Seven-layer networking model (Physical, Data Link, Network, Transport, Session, Presentation, Application)
- **DNS (Domain Name System)**: System translating domain names to IP addresses
- **Load Balancer**: Distributes traffic across multiple servers
- **NAT (Network Address Translation)**: Translating private IPs to public IPs

---

## Project Management Concepts

### General PM

- **Change Control Board**: Group responsible for approving project changes
- **EVM (Earned Value Management)**: Project performance measurement technique
- **Executive Sponsor**: Senior executive who champions and supports the project
- **Milestone**: Significant event or achievement in project timeline
- **PMO (Project Management Office)**: Organizational unit providing PM standards, processes, templates, and support
- **Portfolio Management**: Managing collection of projects and programs to achieve strategic objectives
- **Program Management**: Coordinated management of related projects to deliver benefits
- **Project Charter**: Document authorizing project and defining objectives
- **Risk Register**: Document tracking identified risks and responses
- **Scope Creep**: Uncontrolled expansion of project scope
- **Stage Gate**: Go/no-go decision point at key project milestones
- **Steering Committee**: Executive oversight body making strategic project decisions
- **Stakeholder**: Individual or group with interest in project outcome
- **Stakeholder Register**: Document identifying stakeholders, their interests, influence, and engagement needs
- **Power/Interest Grid**: Matrix categorizing stakeholders by power and interest levels
- **Influence/Impact Matrix**: Matrix categorizing stakeholders by influence and project impact
- **Triple Constraint**: Scope, Time, Cost (fundamental project constraints)
- **WBS (Work Breakdown Structure)**: Hierarchical decomposition of project work

### IT Project Management

- **SDLC Integration**: Aligning project management with software development lifecycle
- **Technical Debt**: Shortcuts in development that create future maintenance burden
- **Vendor Management**: Managing relationships with third-party vendors
- **Watermarking**: Tracking high-water mark for incremental data processing

### Methodologies

- **Agile**: Iterative, incremental development methodology
- **Kanban**: Visual workflow management method
- **PRINCE2**: Process-based project management methodology
- **SAFe (Scaled Agile Framework)**: Framework for scaling Agile to enterprise
- **Scrum**: Agile framework with defined roles, events, artifacts
- **Waterfall**: Sequential project management methodology
- **Product Manager**: Role responsible for product strategy, roadmap, and feature prioritization
- **Product Owner**: Scrum role owning product backlog, prioritizing work, defining acceptance criteria
- **ADKAR Model**: Change management model (Awareness, Desire, Knowledge, Ability, Reinforcement)
- **Kotter's 8-Step Process**: Change management framework for organizational transformation

### Roadmap

- **Roadmap**: High-level strategic view of project goals, milestones, timeline
- **Project Plan**: Detailed tactical plan with tasks, schedules, resources
- **Milestone**: Significant progress point or achievement
- **Dependency**: Relationship where one task must complete before another starts
- **Critical Path**: Sequence of dependent tasks that determines minimum project duration
- **Gantt Chart**: Visual timeline showing project tasks, durations, and dependencies
- **SMART Objectives**: Specific, Measurable, Achievable, Relevant, Time-bound goals
- **Product Roadmap**: Strategic plan for product development, features, and releases
- **Release Roadmap**: Timeline showing planned product releases and features
- **Theme-Based Roadmap**: Roadmap organized by themes or strategic initiatives
- **Now-Next-Later Roadmap**: Agile roadmap format showing current, upcoming, and future work

---

## Business & Finance Concepts

### Financial Management

- **Budget**: Financial plan allocating resources for specific period or project
- **CapEx (Capital Expenditure)**: One-time investment in assets (hardware, infrastructure)
- **Chargeback**: Allocating IT costs to business units based on actual usage
- **Cost Variance (CV)**: Difference between earned value and actual cost (CV = EV - AC)
- **EAC (Estimate At Completion)**: Projected total cost at project completion
- **ETC (Estimate To Complete)**: Projected cost to complete remaining work
- **OpEx (Operational Expenditure)**: Ongoing operational costs (cloud services, licenses)
- **ROI (Return on Investment)**: Measure of profitability (gain - cost) / cost
- **Showback**: Reporting IT costs to business units without actual charge
- **TCO (Total Cost of Ownership)**: Total cost including acquisition, operation, maintenance
- **Variance**: Difference between planned and actual performance

### Business Metrics

- **KPI (Key Performance Indicator)**: Measurable value demonstrating effectiveness
- **SLA (Service Level Agreement)**: Contractual commitment to service level
- **SLO (Service Level Objective)**: Internal target for service reliability/performance
- **SLI (Service Level Indicator)**: Measurable aspect of service performance
- **Business Value**: Benefit delivered to organization (revenue, cost savings, efficiency)

### Procurement & Contracts

- **Master Service Agreement (MSA)**: Framework agreement governing multiple projects
- **RFP (Request for Proposal)**: Document soliciting vendor proposals
- **RFQ (Request for Quote)**: Document requesting pricing information
- **SOW (Statement of Work)**: Detailed description of work to be performed
- **Vendor Management**: Process of managing relationships with third-party suppliers

### Project Financials

- **Baseline**: Approved plan used as reference for performance measurement
- **Budget Approval**: Authorization to spend allocated funds
- **Cost Control**: Process of monitoring and managing project costs
- **Forecast**: Prediction of future project performance based on current trends
- **Funding**: Financial resources allocated to project

### Enterprise Finance

- **Allocation**: Distribution of costs to departments, projects, or cost centers
- **Cost Center**: Organizational unit that incurs costs but doesn't generate revenue
- **Depreciation**: Accounting method allocating asset cost over useful life
- **FinOps**: Financial operations practice optimizing cloud spending
- **Profit Center**: Organizational unit that generates revenue and profit

---

## Acronyms & Abbreviations

### Cloud & Infrastructure
- **ADF**: Azure Data Factory
- **ADLS**: Azure Data Lake Storage
- **API**: Application Programming Interface
- **CDN**: Content Delivery Network
- **CI/CD**: Continuous Integration / Continuous Deployment
- **IaC**: Infrastructure as Code
- **mTLS**: Mutual TLS
- **OIDC**: OpenID Connect
- **SLA**: Service Level Agreement
- **SLO**: Service Level Objective
- **VNet**: Virtual Network
- **WAF**: Web Application Firewall

### Data
- **BI**: Business Intelligence
- **CDC**: Change Data Capture
- **CLS**: Column-Level Security
- **DLT**: Delta Live Tables
- **ELT**: Extract, Load, Transform
- **ETL**: Extract, Transform, Load
- **PII**: Personally Identifiable Information
- **RLS**: Row-Level Security

### Software
- **API**: Application Programming Interface
- **CQRS**: Command Query Responsibility Segregation
- **DDD**: Domain-Driven Design
- **DNS**: Domain Name System
- **MTTR**: Mean Time To Recovery
- **OSI**: Open Systems Interconnection
- **SDLC**: Software Development Life Cycle

### Project Management
- **ADKAR**: Awareness, Desire, Knowledge, Ability, Reinforcement (change management model)
- **EVM**: Earned Value Management
- **KPI**: Key Performance Indicator
- **PMO**: Project Management Office
- **RFP**: Request for Proposal
- **RFQ**: Request for Quote
- **SLA**: Service Level Agreement
- **SLO**: Service Level Objective
- **SMART**: Specific, Measurable, Achievable, Relevant, Time-bound (goal setting)
- **SOW**: Statement of Work
- **TCO**: Total Cost of Ownership
- **WBS**: Work Breakdown Structure

### Business & Finance
- **CapEx**: Capital Expenditure
- **EAC**: Estimate At Completion
- **ETC**: Estimate To Complete
- **MSA**: Master Service Agreement
- **OpEx**: Operational Expenditure
- **ROI**: Return on Investment
- **TCO**: Total Cost of Ownership

---

> **Note**: For detailed explanations and implementation patterns, see the respective concept documentation in [concepts/](concepts/).

