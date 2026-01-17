# Cloud Optimization — Performance, Cost & Developer Experience

**Scope**: Optimization practices for cloud platforms with emphasis on Azure/Microsoft services: performance, cost, and developer experience/maintainability.

**Purpose**: Use this when optimizing cloud workloads for performance, cost, and maintainability. For architecture patterns, see [Cloud Architecture](architecture.md). For Azure-specific patterns, see [Azure Architecture](azure_architecture.md).

## Table of Contents

- [1. Performance Optimization](#1-performance-optimization)
- [2. Cost Optimization](#2-cost-optimization)
- [3. Developer Experience & Maintainability](#3-developer-experience--maintainability)
- [4. Monitoring & Observability](#4-monitoring--observability)
- [5. Optimization Checklist](#5-optimization-checklist)

---

## 1. Performance Optimization

### Compute Optimization

**Right-Sizing Resources**
- **App Service**: Start with Basic/Standard SKU, scale up based on metrics (CPU, memory, response time)
- **Azure Functions**: Right-size memory allocation (affects CPU allocation), monitor execution time
- **Virtual Machines**: Use VM size recommendations, monitor CPU/memory utilization, downsize if underutilized
- **AKS**: Use cluster autoscaler, configure node pool sizes appropriately, use spot instances for non-critical workloads
- **Synapse SQL**: Pause/resume for cost, right-size DWU based on workload patterns

**Scaling Strategies**
- **Horizontal scaling**: Scale out (add instances) for stateless workloads, better than scale up
- **Vertical scaling**: Scale up (larger instance) for stateful workloads, memory-intensive applications
- **Auto-scaling**: Configure based on metrics (CPU, memory, queue length, custom metrics)
- **Predictive scaling**: Use scheduled scaling for known patterns (business hours, batch processing)

**Caching Strategies**
- **Application caching**: Azure Cache for Redis (session state, frequently accessed data)
- **CDN**: Azure Front Door / Azure CDN (static assets, API responses, global distribution)
- **Database caching**: Query result caching, materialized views, read replicas
- **Power BI**: Use aggregations, DirectQuery optimization, import mode for frequently accessed data

### Data & Storage Optimization

**Data Access Patterns**
- **Partitioning**: Partition by date, tenant, or logical boundaries (enables partition elimination)
- **Indexing**: Create appropriate indexes (clustered, non-clustered, columnstore for analytics)
- **Query optimization**: Use query hints, statistics, execution plans, avoid N+1 queries
- **Connection pooling**: Reuse database connections, configure pool sizes appropriately

**Storage Optimization**
- **ADLS Gen2**: Optimize file sizes (128MB-1GB target), use Delta format, Z-ordering
- **Blob Storage**: Use appropriate access tiers (hot, cool, archive), lifecycle policies
- **Cosmos DB**: Choose right partition key, optimize RU consumption, use change feed efficiently
- **SQL Database**: Use columnstore indexes for analytics, optimize table distribution (Synapse)

**Data Transfer Optimization**
- **Reduce data movement**: Pushdown filters, incremental loads, CDC where possible
- **Compression**: Enable compression for data transfer, use compressed formats (Parquet, Delta)
- **Batch operations**: Batch API calls, use bulk operations, minimize round trips
- **Geographic proximity**: Deploy resources in same region, use availability zones for redundancy

### Network Optimization

**Latency Reduction**
- **Regional deployment**: Deploy resources in same region, use regional pairs for DR
- **CDN**: Use Azure Front Door / CDN for static content, API responses
- **Private endpoints**: Use private endpoints for lower latency, reduced egress costs
- **Connection reuse**: HTTP keep-alive, connection pooling, persistent connections

**Bandwidth Optimization**
- **Compression**: Enable compression (gzip, brotli) for HTTP responses
- **Data deduplication**: Avoid transferring duplicate data, use change feeds
- **Caching**: Cache responses at edge (CDN), application level, database level
- **Pagination**: Implement pagination for large result sets, use cursor-based pagination

### Application Performance

**Code Optimization**
- **Async/await**: Use asynchronous operations (I/O-bound operations), avoid blocking calls
- **Parallel processing**: Parallelize independent operations, use Task.WhenAll, parallel loops
- **Lazy loading**: Load data on demand, avoid eager loading of unnecessary data
- **Memory management**: Dispose resources properly, avoid memory leaks, use object pooling

**API Optimization**
- **Response compression**: Enable compression middleware, compress large responses
- **Pagination**: Implement pagination, limit result sets, use cursor-based pagination
- **Field selection**: Allow field selection (GraphQL-style), return only needed fields
- **Rate limiting**: Implement rate limiting, use API Management policies

**Database Optimization**
- **Query optimization**: Use indexes, avoid SELECT *, optimize JOINs, use EXISTS vs IN
- **Connection management**: Use connection pooling, limit connection lifetime
- **Batch operations**: Batch inserts/updates, use bulk operations, minimize round trips
- **Read replicas**: Use read replicas for read-heavy workloads, offload reporting queries

---

## 2. Cost Optimization

### Compute Cost Optimization

**Right-Sizing**
- **Monitor utilization**: Track CPU, memory, disk I/O, network utilization
- **Downsize underutilized resources**: Reduce VM sizes, App Service plans, Function memory
- **Reserved Instances**: Use Reserved Instances for predictable workloads (1-3 year commitments)
- **Spot Instances**: Use spot VMs for fault-tolerant, flexible workloads (up to 90% savings)

**Scaling Optimization**
- **Auto-scaling**: Scale down during off-peak hours, scale to zero for serverless
- **Scheduled scaling**: Scale down during nights/weekends, scale up for business hours
- **Pause/resume**: Pause Synapse SQL pools, AKS clusters during off-hours
- **Container optimization**: Use smaller base images, multi-stage builds, remove unused dependencies

**Serverless Optimization**
- **Azure Functions**: Use Consumption plan for sporadic workloads, Premium for always-on
- **Execution time**: Optimize function execution time (affects cost), minimize cold starts
- **Concurrency**: Configure appropriate concurrency limits, avoid over-provisioning
- **Durable Functions**: Use for long-running workflows, avoid polling patterns

### Storage Cost Optimization

**Storage Tiers**
- **Hot tier**: Frequently accessed data, lowest access cost
- **Cool tier**: Infrequently accessed data (30+ days), lower storage cost
- **Archive tier**: Rarely accessed data (180+ days), lowest storage cost, retrieval cost
- **Lifecycle policies**: Automatically move data between tiers based on age/access patterns

**Data Lifecycle Management**
- **Retention policies**: Delete old data per compliance requirements, archive cold data
- **Data deduplication**: Remove duplicate data, use change feeds instead of full loads
- **Compression**: Compress data at rest (Parquet, Delta), reduce storage footprint
- **Partition cleanup**: Delete old partitions, implement partition-level retention

**Backup Optimization**
- **Backup retention**: Align retention with compliance requirements, avoid over-retention
- **Backup frequency**: Balance RPO requirements with backup costs
- **Backup storage**: Use appropriate backup storage tiers, archive old backups
- **Point-in-time recovery**: Configure appropriate retention periods

### Network Cost Optimization

**Data Transfer Costs**
- **Regional deployment**: Deploy resources in same region (no egress charges)
- **Private endpoints**: Use private endpoints (reduced egress, lower latency)
- **CDN**: Use CDN for content delivery (reduced origin load, lower egress)
- **Compression**: Compress data transfers, reduce bandwidth consumption

**Egress Optimization**
- **Minimize egress**: Cache data, use regional resources, avoid cross-region transfers
- **Peering**: Use VNet peering for inter-region communication (lower cost than internet)
- **Private Link**: Use Private Link for PaaS services (reduced egress costs)
- **Data residency**: Keep data in same region, comply with data residency requirements

### Monitoring & Cost Management

**Cost Monitoring**
- **Cost alerts**: Set up budget alerts, cost anomaly detection
- **Cost analysis**: Use Cost Management + Billing for cost breakdown, resource-level costs
- **Tags**: Use tags for cost allocation, track costs by project/department/environment
- **Cost optimization recommendations**: Review Azure Advisor recommendations

**Resource Management**
- **Resource cleanup**: Delete unused resources, orphaned resources, test resources
- **Resource groups**: Organize resources logically, use resource group policies
- **Automation**: Automate resource cleanup, scheduled shutdowns, lifecycle management
- **Governance**: Use Azure Policy to enforce cost controls, prevent over-provisioning

---

## 3. Developer Experience & Maintainability

### Code Organization & Structure

**Modular Design**
- **Separation of concerns**: Separate business logic, data access, presentation layers
- **Dependency injection**: Use DI containers (built-in .NET DI, Autofac), improve testability
- **Configuration management**: Externalize configuration (App Settings, Key Vault, environment variables)
- **Error handling**: Centralized error handling, consistent error responses, logging

**Code Quality**
- **Static analysis**: Use linters, code analyzers (SonarQube, CodeQL), enforce standards
- **Code reviews**: Peer reviews, automated checks, enforce review requirements
- **Documentation**: Code comments, API documentation, architecture diagrams
- **Testing**: Unit tests, integration tests, end-to-end tests, test coverage

### Infrastructure as Code (IaC)

**Terraform/Bicep**
- **Version control**: Store IaC in Git, version infrastructure changes
- **Modularity**: Create reusable modules, parameterize configurations
- **Environment promotion**: Use same code for dev/test/prod, parameterize differences
- **State management**: Secure state storage, enable state locking, backup state

**Best Practices**
- **Idempotency**: Ensure IaC is idempotent, safe to run multiple times
- **Validation**: Validate templates before deployment, use linting tools
- **Documentation**: Document modules, parameters, dependencies
- **Testing**: Test IaC changes in non-production environments first

### CI/CD Optimization

**Pipeline Efficiency**
- **Parallel execution**: Run independent jobs in parallel, reduce pipeline duration
- **Caching**: Cache dependencies (NuGet, npm, Docker layers), reduce build times
- **Incremental builds**: Only build changed components, use build artifacts
- **Conditional execution**: Skip stages when not needed, use conditions

**Deployment Strategies**
- **Blue-green deployment**: Zero-downtime deployments, instant rollback
- **Canary deployment**: Gradual rollout, monitor metrics, rollback if issues
- **Feature flags**: Deploy code behind flags, enable gradually, instant rollback
- **Database migrations**: Version migrations, backward-compatible changes, rollback scripts

**Azure DevOps Best Practices**
- **Pipeline templates**: Reusable pipeline templates, reduce duplication
- **Variable groups**: Centralize configuration, use Key Vault integration
- **Approval gates**: Manual approvals for production, automated quality gates
- **Release notes**: Automate release notes, track changes, communicate updates

### Observability & Debugging

**Logging**
- **Structured logging**: Use structured logs (JSON), consistent log levels, correlation IDs
- **Application Insights**: Use Application Insights for .NET applications, custom telemetry
- **Log Analytics**: Centralize logs in Log Analytics, use KQL queries, create alerts
- **Log retention**: Configure appropriate retention, archive old logs, comply with requirements

**Tracing**
- **Distributed tracing**: Use Application Insights distributed tracing, correlation IDs
- **Request tracking**: Track requests across services, identify bottlenecks
- **Performance profiling**: Profile slow operations, identify performance issues
- **Dependency tracking**: Track external dependencies, identify slow dependencies

**Debugging Tools**
- **Remote debugging**: Use remote debugging for cloud resources, Application Insights Snapshot Debugger
- **Diagnostic tools**: Use Azure diagnostic tools, Application Insights Profiler
- **Error tracking**: Track exceptions, use Application Insights exception tracking
- **Performance monitoring**: Monitor performance metrics, set up alerts

### Documentation & Knowledge Management

**Code Documentation**
- **API documentation**: OpenAPI/Swagger for APIs, keep documentation up to date
- **Code comments**: Document complex logic, explain "why" not "what"
- **README files**: Project setup, configuration, deployment instructions
- **Architecture diagrams**: Document system architecture, data flows, dependencies

**Runbooks & Procedures**
- **Runbooks**: Document operational procedures, troubleshooting steps
- **Incident response**: Document incident response procedures, escalation paths
- **On-call procedures**: Document on-call responsibilities, contact information
- **Knowledge base**: Centralize knowledge, use Confluence, SharePoint, or wiki

---

## 4. Monitoring & Observability

### Metrics & Alerts

**Key Metrics**
- **Performance**: Response time, throughput, error rate, availability
- **Resource utilization**: CPU, memory, disk I/O, network I/O
- **Cost metrics**: Resource costs, data transfer costs, storage costs
- **Business metrics**: User activity, transaction volume, revenue impact

**Alert Configuration**
- **Alert rules**: Configure alerts for critical metrics, avoid alert fatigue
- **Alert grouping**: Group related alerts, reduce noise
- **Alert actions**: Automated actions (auto-scaling, notifications), manual intervention
- **Alert channels**: Email, SMS, Teams, PagerDuty, ITSM integration

### Dashboards

**Operational Dashboards**
- **Application health**: Service health, error rates, performance metrics
- **Infrastructure health**: Resource utilization, capacity, availability
- **Cost dashboards**: Cost trends, budget tracking, cost by resource
- **Business dashboards**: Business metrics, user activity, revenue impact

**Power BI Dashboards**
- **Data sources**: Connect to Log Analytics, Application Insights, Azure Monitor
- **Real-time dashboards**: Use DirectQuery for real-time data, import for historical
- **Custom metrics**: Create custom metrics, calculated columns, measures
- **Sharing**: Share dashboards with stakeholders, set up subscriptions

### Application Insights

**Telemetry Collection**
- **Automatic collection**: Request tracking, dependency tracking, exceptions
- **Custom telemetry**: Custom events, metrics, traces, user tracking
- **Performance counters**: System metrics, custom performance counters
- **Logs**: Application logs, structured logging, correlation IDs

**Analysis & Insights**
- **Application Map**: Visualize application topology, dependencies
- **Performance**: Identify slow operations, bottlenecks, optimization opportunities
- **Failures**: Track exceptions, failed requests, error patterns
- **Usage**: User activity, feature usage, adoption metrics

---

## 5. Optimization Checklist

### Performance

- [ ] Right-size compute resources (CPU, memory, disk)
- [ ] Configure auto-scaling based on metrics
- [ ] Implement caching strategies (Redis, CDN, application cache)
- [ ] Optimize database queries (indexes, query plans, connection pooling)
- [ ] Partition data appropriately (date, tenant, logical boundaries)
- [ ] Enable compression (HTTP responses, data transfer)
- [ ] Use CDN for static assets and API responses
- [ ] Optimize data transfer (reduce movement, batch operations)
- [ ] Monitor performance metrics, set up alerts
- [ ] Profile slow operations, optimize bottlenecks

### Cost

- [ ] Monitor resource utilization, downsize underutilized resources
- [ ] Use Reserved Instances for predictable workloads
- [ ] Use spot instances for fault-tolerant workloads
- [ ] Configure auto-scaling to scale down during off-peak hours
- [ ] Implement storage lifecycle policies (hot/cool/archive)
- [ ] Delete unused resources, orphaned resources
- [ ] Use appropriate storage tiers based on access patterns
- [ ] Minimize data egress (regional deployment, private endpoints)
- [ ] Set up cost alerts, budget alerts
- [ ] Review Azure Advisor recommendations regularly

### Developer Experience

- [ ] Use Infrastructure as Code (Terraform, Bicep)
- [ ] Implement CI/CD pipelines (Azure DevOps, GitHub Actions)
- [ ] Externalize configuration (App Settings, Key Vault)
- [ ] Implement structured logging, centralized logging
- [ ] Set up Application Insights for telemetry
- [ ] Document APIs (OpenAPI/Swagger)
- [ ] Create runbooks for operational procedures
- [ ] Implement feature flags for gradual rollouts
- [ ] Use dependency injection, modular design
- [ ] Write tests (unit, integration, end-to-end)

### Monitoring & Observability

- [ ] Set up Application Insights for applications
- [ ] Configure Log Analytics for centralized logging
- [ ] Create operational dashboards (Azure Monitor, Power BI)
- [ ] Set up alerts for critical metrics
- [ ] Implement distributed tracing
- [ ] Track business metrics alongside technical metrics
- [ ] Document monitoring procedures, runbooks
- [ ] Set up cost monitoring dashboards
- [ ] Configure health checks, availability monitoring
- [ ] Implement error tracking, exception monitoring

---

> **Note**: For architecture patterns, see [Cloud Architecture](architecture.md). For Azure-specific patterns, see [Azure Architecture](azure_architecture.md). For infrastructure patterns, see [Cloud Infrastructure & Operations](infrastructure.md).

