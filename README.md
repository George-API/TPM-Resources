# IT Project Management Reference

Comprehensive reference for IT project management, covering data engineering, cloud architecture, software development, and programming syntax. This resource provides useful context and serves as a secondary reference for initial or quick lookups, designed to be used alongside authoritative documentation and official sources.

**General Resources**: [Terminology](TERMINOLOGY.md) | [ITPM Resources](RESOURCES.md) 

---

## [Concepts](concepts/)

### [Cloud](concepts/cloud/)
- [Cloud Architecture](concepts/cloud/architecture.md) - Identity, networking, APIs, messaging, reliability
- [Cloud Infrastructure & Operations](concepts/cloud/infrastructure.md) - Containers, serverless, CI/CD, compliance
- [Cloud Optimization](concepts/cloud/optimization.md) - Performance, cost, and developer experience optimization
- [Azure Data Platform](concepts/cloud/azure_architecture.md) - ADF, ADLS, Synapse
- [Microsoft Fabric](concepts/cloud/fabric_architecture.md) - OneLake, Lakehouse, Warehouse
- [Azure Databricks](concepts/cloud/databricks_architecture.md) - Workspace, Delta Lake, Unity Catalog

### [Data](concepts/data/)
- [Analytics Engineering](concepts/data/analytics_eng.md) - Transformation layer, semantic layer, BI modeling
- [Power BI Optimization](concepts/data/powerbi_optimization.md) - Optimizing analytics engineering for Power BI reports
- [Data Analytics](concepts/data/analytics.md) - Statistical analysis, EDA, forecasting
- [Database Management](concepts/data/database.md) - RDBMS design, indexing, query optimization
- [Data Engineering](concepts/data/engineering.md) - ETL/ELT, orchestration, transformation
- [Data Governance](concepts/data/governance.md) - Operating models, metadata, catalog
- [Data Operations](concepts/data/operations.md) - Quality, integrity, lifecycle, reliability
- [Data Security](concepts/data/security.md) - Access control, encryption, privacy

### [Software](concepts/software/)
- [Software Architecture](concepts/software/architecture.md) - System patterns, microservices, event-driven
- [Software Design](concepts/software/design.md) - Code patterns, SOLID, DDD, OOP/FP
- [DevOps](concepts/software/devops.md) - CI/CD pipelines, IaC, deployment strategies
- [Azure DevOps](concepts/software/azure_devops.md) - Azure DevOps platform features and usage
- [DSA Overview](concepts/software/dsa.md) - Data structures and algorithms
- [OSI Model](concepts/software/osi.md) - Troubleshooting lens (OSI layers)
- [SDLC](concepts/software/sdlc.md) - Development life cycle, methodologies

### [Project Management](concepts/project_management/)
- [General Project Management](concepts/project_management/general.md) - Core PM concepts, lifecycle, constraints, risk
- [IT Project Management](concepts/project_management/itpm.md) - IT-specific patterns, SDLC integration, technical debt
- [Project Management Methodologies](concepts/project_management/methodologies.md) - Agile, Scrum, Kanban, Waterfall, scaling
- [Project Roadmap Development](concepts/project_management/roadmap.md) - Strategic planning, milestones
- [Stakeholder Engagement](concepts/project_management/stakeholders.md) - Enterprise public sector stakeholder management
- [Enterprise Reporting](concepts/project_management/reporting.md) - Power BI reports, reporting best practices for Ontario public sector
- [PM Tools & Resources](concepts/project_management/tools_resources.md) - PM tools and when to use them

---

## [Programming](programming/)

### Languages
- **[C/C++](programming/c/)** - Systems programming, memory management, performance
- **[C#](programming/csharp/)** - Enterprise platforms, APIs, Azure services
- **[Go](programming/go/)** - DevOps, infrastructure automation, cloud-native development
- **[Java](programming/java/)** - Enterprise platforms, Spring ecosystem
- **[JavaScript](programming/javascript/)** - Frontend frameworks, web development
- **[Python](programming/python/)** - Data analysis, ML, web development
- **[R](programming/r/)** - Statistical analysis, modeling
- **[Rust](programming/rust/)** - Memory-safe systems, security-critical components
- **[SQL](programming/sql/)** - Database queries, data manipulation
- **[TypeScript](programming/typescript/)** - Type-safe JavaScript, web development

### Utilities
- **[Bicep](programming/bicep/)** - Azure IaC (Bicep DSL)
- **[Excel](programming/excel/)** - Data analysis, ETL, Power Query
- **[Git](programming/git/)** - Version control
- **[Terraform](programming/terraform/)** - Azure IaC (HCL)
- **[Terminal](programming/terminal/)** - Bash commands

---

## [Trends](trends/)

- **[IT 2026 Executive Analysis](trends/TRENDS_ANALYSIS.md)** - Strategic technology trends, cybersecurity, AI transformation
- **[State of IT 2026 Report](trends/TRENDS.MD)** - Comprehensive industry analysis and risk assessment

---

## Programming Language Usage

**Mental model**: Use the highest-level language that does not hide risks you actually care about; drop lower only when correctness, scale, or performance demands it.

- **Python** - Data analysis, automation, scripting, ML
- **PySpark** - Large-scale data engineering, distributed processing, ETL/ELT pipelines
- **R** - Statistical analysis, modeling, forecasting, quantitative research
- **JavaScript/TypeScript** - Frontend and full-stack web applications
- **Go** - DevOps tooling, infrastructure automation, cloud-native services, high-performance backends
- **C#** - Azure-centric enterprise platforms, APIs, identity-aware services, integrations
- **Java** - Large-scale enterprise platforms, legacy systems, Spring/JVM ecosystems
- **Rust** - Memory-safe systems, security-critical components, high-assurance services
- **C++** - Performance-critical engines, real-time systems, low-level platform components
- **C** - Embedded systems, OS-level components, firmware, minimal runtime environments
