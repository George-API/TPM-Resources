# Programming Reference

Quick reference for data, cloud & software engineering concepts and programming syntax.

## Concepts

### Cloud
- [Cloud Architecture](concepts/cloud/architecture.md) - Identity, networking, APIs, messaging, reliability, multi-tenant patterns
- [Cloud Infrastructure & Operations](concepts/cloud/infrastructure.md) - Containers, serverless, CI/CD, compliance, operations
- [Azure Data Platform](concepts/cloud/azure_architecture.md) - ADF, ADLS, Synapse implementation
- [Microsoft Fabric](concepts/cloud/fabric_architecture.md) - OneLake, Lakehouse, Warehouse implementation

### Data
- [Analytics Engineering](concepts/data/analytics_eng.md) - Transformation layer, semantic layer, BI modeling
- [Data Analytics](concepts/data/analytics.md) - Statistical analysis, EDA, forecasting, experimentation
- [Database Management](concepts/data/database.md) - RDBMS design, indexing, query optimization, transactions
- [Data Engineering](concepts/data/engineering.md) - ETL/ELT, orchestration, transformation, data modeling
- [Data Governance](concepts/data/governance.md) - Operating models, metadata, catalog, policies
- [Data Operations](concepts/data/operations.md) - Quality, integrity, lifecycle, reliability, Data SRE
- [Data Security](concepts/data/security.md) - Access control, encryption, privacy, compliance

### Software
- [Software Architecture](concepts/software/architecture.md) - System-level patterns, microservices, event-driven
- [Software Design](concepts/software/design.md) - Code-level patterns, SOLID, DDD, OOP/FP
- [DevOps](concepts/software/devops.md) - CI/CD pipelines, Azure DevOps
- [DSA Overview](concepts/software/dsa.md) - Data structures and algorithms
- [OSI Model](concepts/software/osi.md) - Troubleshooting lens (OSI layers, cloud emphasis)
- [SDLC](concepts/software/sdlc.md) - Development life cycle, methodologies, best practices

## Languages

### C
- [C](languages/c/c.md) - Systems programming, memory management, pointers, standard library

### C++
- [C++](languages/cpp/cpp.md) - OOP, templates, STL, RAII, smart pointers, modern C++ features

### C#
- [C#](languages/csharp/csharp.md) - Syntax: types, operators, control flow, OOP, LINQ, async/await
- [ASP.NET Core](languages/csharp/aspnet.md) - REST, SOAP, GraphQL, DI, middleware, EF Core, authentication
- [Azure & Fabric Integration](languages/csharp/azure.md) - Azure services, Managed Identity, Application Insights, Fabric API

### Java
- [Java](languages/java/java.md) - Collections, I/O, JSON, HTTP, SOAP, database access
- [Spring Boot](languages/java/springboot.md) - REST APIs, DI, security, JPA, testing, configuration
- [JVM](languages/java/jvm.md) - Memory management, GC, performance tuning, monitoring, troubleshooting
- [Patterns](languages/java/patterns.md) - Enterprise patterns, best practices

### Python
- [Core](languages/python/core.md) - Syntax: types, operators, control flow, functions, classes
- [DSA](languages/python/dsa.md) - Data structures and algorithms (Python syntax)
- [Patterns](languages/python/patterns.md) - Common patterns, idioms, best practices
- [Django](languages/python/packages/django.md) - Models, views, URLs, forms, admin, ORM, auth
- [FastAPI](languages/python/packages/fastapi.md) - Routing, models, validation, auth, DB patterns
- [Pandas](languages/python/packages/pandas.md) - Quick reference and ETL patterns
- [PySpark](languages/python/pyspark.md) - PySpark syntax for Fabric/Delta Lake
- [Scikit-learn](languages/python/packages/sklearn.md) - Machine learning: preprocessing, models, pipelines
- [Visualization](languages/python/packages/visualization.md) - Matplotlib and Seaborn plotting

### R
- [Core](languages/r/core.md) - Syntax: types, vectors, data frames, functions, base R
- [Tidyverse](languages/r/tidyverse.md) - tidyr, stringr, lubridate, readr, forcats
- [dplyr](languages/r/dplyr.md) - dplyr syntax reference for data manipulation
- [Patterns](languages/r/patterns.md) - Common patterns, workflows, best practices

### Rust
- [Rust](languages/rust/rust.md) - Ownership, borrowing, lifetimes, traits, error handling, concurrency

### SQL
- [Core](languages/sql/core.md) - Core syntax, operation flow, basic queries, DDL/DML
- [Patterns](languages/sql/patterns.md) - Common patterns, window functions, CTEs, data quality

### TypeScript
- [TypeScript](languages/typescript/typescript.md) - Syntax: types, interfaces, classes, generics, modules

---

## Utilities

### Bicep
- [Bicep](utilities/bicep/bicep.md) - Azure IaC: Bicep DSL syntax, resources, modules, deployment

### Excel
- [Excel](utilities/excel/excel.md) - Functions, lookups, formulas, data analysis
- [Power Query](utilities/excel/powerquery.md) - M language, data transformation, ETL patterns

### Git
- [Git](utilities/git/git.md) - Command reference and workflows
- [Best Practices](utilities/git/best_practices.md) - Workflows, configuration, hooks, protocols

### Terraform
- [Terraform](utilities/terraform/terraform.md) - Azure IaC: HCL syntax, resources, modules, state management

### Terminal
- [Terminal](utilities/terminal/terminal.md) - Bash command reference

---

## Language Usage Guide

**Mental model**: Use the highest-level language that does not hide risks you actually care about; drop lower only when correctness, scale, or performance demands it.

- **Python** - Data analysis, automation, scripting, ML
- **PySpark** - Large-scale data engineering, distributed processing, ETL/ELT pipelines
- **R** - Statistical analysis, modeling, forecasting, quantitative research
- **JavaScript/TypeScript** - Frontend and full-stack web applications
- **C#** - Azure-centric enterprise platforms, APIs, identity-aware services, integrations
- **Java** - Large-scale enterprise platforms, legacy systems, Spring/JVM ecosystems
- **Rust** - Memory-safe systems, security-critical components, high-assurance services
- **C++** - Performance-critical engines, real-time systems, low-level platform components
- **C** - Embedded systems, OS-level components, firmware, minimal runtime environments
