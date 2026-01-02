# Cloud Infrastructure & Operations

**Scope**: Infrastructure deployment patterns, container orchestration, serverless, CI/CD, compliance, and operational practices for cloud platforms.

**Purpose**: Use this when deploying infrastructure, managing containers, setting up CI/CD, and operating cloud systems. For application architecture patterns, see [Cloud Architecture Patterns](architecture.md).

## Table of Contents

- [1. Secrets & Keys](#1-secrets--keys)
- [2. Container Orchestration](#2-container-orchestration)
- [3. Serverless Patterns](#3-serverless-patterns)
- [4. CI/CD & GitOps](#4-cicd--gitops)
- [5. Compliance & Governance](#5-compliance--governance)
- [6. Operational Excellence](#6-operational-excellence)

---

## 1. Secrets & Keys

### Key management

- Central keys/certs; app secrets only when unavoidable
- Access via Managed Identity; rotate automatically
- **Secret scanning**: Scan code repos for hardcoded secrets; pre-commit hooks; automated rotation windows

### Confidential computing (when mandated)

- Confidential VMs or confidential containers (use case dependent)
- Always start with threat model + compliance requirement, not trend

---

## 2. Container Orchestration

### Kubernetes patterns

- **Deployments**: Rolling updates, replica sets, pod disruption budgets
- **Services**: ClusterIP (internal), LoadBalancer (external), Ingress (HTTP routing)
- **ConfigMaps/Secrets**: Externalize configuration, mount as volumes or env vars
- **Resource limits**: CPU/memory requests and limits; horizontal pod autoscaling (HPA)
- **Pod security**: Pod Security Standards (restricted, baseline, privileged); security contexts

### Service mesh (when needed)

- **Use cases**: Complex microservices, multi-cloud, advanced traffic management
- **Features**: mTLS, traffic splitting (canary), circuit breaking, distributed tracing
- **Patterns**: Sidecar proxy (Istio, Linkerd), API gateway integration
- **Trade-offs**: Added complexity, resource overhead; use API gateway for simpler cases

### Container security

- **Image scanning**: Scan container images for vulnerabilities; enforce in CI/CD
- **Runtime security**: Pod security policies, network policies, admission controllers
- **Supply chain**: Sign images, use trusted registries, least-privilege base images

### Docker patterns

- **Multi-stage builds**: Separate build and runtime stages; reduce image size
- **Layer caching**: Order Dockerfile instructions by change frequency; leverage cache
- **Base images**: Use official/minimal base images (Alpine, distroless); avoid root user
- **Dockerfile best practices**: Single process per container; use `.dockerignore`; set WORKDIR
- **Image optimization**: Minimize layers; combine RUN commands; remove build dependencies
- **Volumes**: Use named volumes for persistent data; avoid bind mounts in production
- **Networking**: Docker networks for container isolation; bridge network for default
- **Compose**: Docker Compose for local dev; multi-container applications; service dependencies
- **Health checks**: `HEALTHCHECK` instruction; container health monitoring
- **Resource limits**: Set memory/CPU limits; prevent resource exhaustion

---

## 3. Serverless Patterns

### Function-as-a-Service (FaaS)

- **Cold starts**: Minimize dependencies, use provisioned concurrency for critical paths
- **Concurrency limits**: Configure per-function limits; use queues for backpressure
- **State management**: Externalize state (database, cache); functions should be stateless
- **Timeouts**: Set appropriate timeout limits; use durable functions for long-running

### Event-driven serverless

- **Triggers**: HTTP, queue, blob, timer, event grid; choose based on latency requirements
- **Fan-out**: Event Grid → multiple functions; use filters for routing
- **Fan-in**: Multiple sources → single function; use queues for buffering

### Serverless best practices

- **Idempotency**: Design functions to be idempotent; use idempotency keys
- **Error handling**: Dead letter queues for failed invocations; retry policies
- **Cost optimization**: Right-size memory allocation; monitor execution time and invocations

---

## 4. CI/CD & GitOps

### CI/CD pipelines

- **Build**: Compile, test, package artifacts; container image builds
- **Test**: Unit, integration, security scanning; automated test execution
- **Deploy**: Infrastructure provisioning (IaC), application deployment, smoke tests
- **Environments**: Dev, test, staging, production; environment promotion gates

### GitOps patterns

- **Infrastructure as Code**: Version control all infrastructure; declarative definitions
- **GitOps workflow**: Git as source of truth; automated sync to clusters/environments
- **Pull-based**: Operator pulls changes from Git; push-based for traditional CI/CD
- **Rollback**: Revert Git commit; automated rollback on deployment failures

### Testing strategies

- **Shift-left**: Test early in pipeline; static analysis, dependency scanning
- **Integration testing**: Test in isolated environments; mock external dependencies
- **Load testing**: Performance testing before production; identify bottlenecks
- **Chaos engineering**: Test failure scenarios; validate resilience patterns

---

## 5. Compliance & Governance

### Regulatory compliance

- **Data sovereignty**: Enforce geographic data storage; tenant-aware routing for compliance
- **Audit requirements**: Immutable audit logs; retention policies; compliance reporting
- **Privacy regulations**: GDPR, CCPA compliance; data subject rights (access, deletion)
- **Industry standards**: SOC 2, ISO 27001, HIPAA; compliance certifications

### Data governance

- **Data classification**: Classify data by sensitivity (public, internal, confidential, restricted)
- **Retention policies**: Automated data lifecycle; retention schedules; deletion workflows
- **Access governance**: Regular access reviews; least privilege enforcement; RBAC policies
- **Data lineage**: Track data flow; metadata catalog; impact analysis

### Security governance

- **Policy as Code**: Infrastructure policies (Azure Policy, OPA); compliance scanning
- **Security scanning**: Container scanning, dependency scanning, SAST/DAST in CI/CD
- **Vulnerability management**: Regular patching; vulnerability assessment; patch management
- **Incident response**: Security incident playbooks; forensics; breach notification procedures

---

## 6. Operational Excellence

### Runbooks + playbooks

- DLQ handling, replay procedures, incident triage, rollback
- **On-call rotation**: Clear escalation paths; incident severity classification; post-mortem culture

### Audit trails

- Immutable storage for critical logs/events (when required)

### Change management

- Schema changes, API deprecations, topic migrations with comms + timelines
- **Infrastructure as Code**: Version control all infrastructure; GitOps for automated deployments
- **Disaster recovery**: Define RTO/RPO; test failover regularly; automate recovery procedures
- **Deployment strategies**: Blue-green or canary for zero-downtime; feature flags for gradual rollouts; automated rollback triggers

### FinOps

- Cost per tenant/request/pipeline run; budgets + anomaly detection
- Reserved capacity for steady workloads; autoscale for burst
- **Right-sizing**: Regular review of resource utilization; downsize over-provisioned resources
- **Spot instances**: Use for fault-tolerant workloads (batch, dev/test); graceful shutdown handling
- **Tagging strategy**: Consistent tags for cost allocation (team, environment, cost center)

---

> **Note**: For application architecture patterns (identity, networking, APIs, messaging, multi-tenant, reliability), see [Cloud Architecture Patterns](architecture.md).

