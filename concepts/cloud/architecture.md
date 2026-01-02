# Cloud Architecture Patterns

**Scope**: Design patterns and best practices for building cloud applications and platforms (not data-specific).

**Purpose**: Use this when designing and building cloud solutions. For infrastructure deployment and operations, see [Cloud Infrastructure & Operations](infrastructure.md). For troubleshooting existing systems, see the OSI troubleshooting reference.

## Table of Contents

- [1. Identity & Auth](#1-identity--auth)
- [2. Networking & Edge](#2-networking--edge)
- [3. API Platform & Governance](#3-api-platform--governance)
- [4. Messaging & Workflow Patterns](#4-messaging--workflow-patterns)
- [5. Multi-tenant SaaS Patterns](#5-multi-tenant-saas-patterns)
- [6. Reliability Engineering (SRE)](#6-reliability-engineering-sre)
- [7. Reference Blueprints](#7-reference-blueprints)

---

## 1. Identity & Auth

### Workload identity

- **Preferred**: Managed Identity for cloud resources (no secrets)
- **Kubernetes**: Workload Identity / federated credentials

### Auth patterns

- **B2E**: OIDC + group/app-role claims
- **B2C/B2B**: External identity provider for customer-facing apps
- **Token validation**: centralize at API gateway when possible; validate in apps for defense-in-depth
- **Key rotation**: automated cert rotation; avoid long-lived client secrets
- **Zero Trust**: Verify explicitly, least privilege access, assume breach; continuous validation
- **Session management**: Short-lived tokens; refresh token rotation; secure cookie flags (HttpOnly, Secure, SameSite)

---

## 2. Networking & Edge

### Edge routing

- Global CDN/WAF for multi-region failover
- Regional WAF for VNet-centric patterns
- **DDoS protection**: Enable at edge (WAF/CDN); rate limiting per IP/tenant; geo-blocking when appropriate

### Private-only services

- Private Link + disable public network access for data stores
- Private DNS Zones + centralized DNS forwarding

### Egress governance

- Firewall with FQDN tags + TLS inspection (where mandated)
- NAT Gateway for stable outbound IPs (partner allowlists)

### Segmentation

- Separate inbound, app, and data subnets; NSGs as defense-in-depth
- **Health checks**: Liveness/readiness probes; health endpoints for load balancers; dependency health checks

### Multi-region connectivity

- Virtual WAN for branch connectivity; hub-spoke for simpler estates
- **Caching**: Application-level caching (Redis) for read-heavy workloads; CDN for static assets

> **Troubleshooting**: For networking troubleshooting (DNS, routing, private endpoints), see [OSI Model](../software/osi.md).

---

## 3. API Platform & Governance

### API Gateway roles

- **North-south**: external APIs, policies, developer portal
- **East-west**: internal APIs, mTLS, routing, rate limiting
- **Service mesh**: Consider for complex microservices (traffic management, observability, security policies)

### Policies (enterprise essentials)

- JWT validation, quota/rate limit, IP allowlist, correlation headers
- Request/response transformation (avoid heavy transforms at gateway)
- **Versioning**: URL (`/v1`), header, or query param; deprecation policy
- **Security**: OWASP top 10 protection (injection, XSS, broken auth); request size limits

### Observability

- Standard correlation headers: `traceparent` + `x-correlation-id`
- Log request IDs + consumer/app IDs + subscription IDs
- **Distributed tracing**: OpenTelemetry for end-to-end request flows across services
- **Structured logging**: JSON logs with consistent schema; avoid PII in logs
- **Metrics**: Export to central platform (Prometheus-compatible); cardinality limits

---

## 4. Messaging & Workflow Patterns

### Message guarantees

- **Exactly-once is rare**: design for at-least-once + idempotent consumers

### Queue/Event patterns

- **Sessions**: ordered processing per key (per user/tenant/entity)
- **Duplicate detection**: time-window for producer-side help (still keep consumer idempotent)
- **DLQ strategy**: classify faults (transient vs permanent), automate re-drive tooling
- **Event schema**: standardize (CloudEvents where practical)
- **Batch processing**: Batch messages for throughput; balance latency vs efficiency; handle partial batch failures
- **Message ordering**: Guarantee ordering only when required (sessions); accept out-of-order for scalability

### Orchestration

- **Code-first**: Durable Functions for saga/orchestration with replay-safe activities
- **Low-code**: Logic Apps for connector-heavy workflows + approvals + B2B

### Outbox + Inbox

- **Outbox**: DB transaction writes event to outbox table; publisher relays to bus
- **Inbox**: consumer stores processed message IDs to ensure idempotency

---

## 5. Multi-tenant SaaS Patterns

### Tenant isolation models

- **Shared DB + tenant_id**: cheapest; strict RBAC + row-level security
- **DB-per-tenant**: strong isolation; higher ops/cost
- **Hybrid**: shared for small tenants, dedicated for large (enterprise)

### Tenant routing

- Tenant context from JWT claim → route to correct data partition / connection string
- **Tenant lifecycle**: Automated onboarding/offboarding; data isolation verification; tenant data deletion workflows

### Rate limiting

- Per-tenant quotas in API gateway + service-level throttles; protect noisy neighbors

### Data protection

- Per-tenant encryption keys (advanced) when required by compliance
- **Data residency**: Enforce geographic data storage requirements; tenant-aware routing for compliance

---

## 6. Reliability Engineering (SRE)

### SLOs first

- Define SLI: availability, latency, error rate, freshness (data), backlog (queues)
- Alerts based on error budget burn, not raw metrics

### Resilience patterns

- **Bulkheads**: separate thread pools per dependency or workload class
- **Timeouts everywhere**: HTTP, DB, queue handlers
- **Circuit breakers** + retry budgets (avoid retry storms)
- **Retry strategy**: Exponential backoff with jitter; cap max retries; use retry-after headers

### Backpressure

- Autoscale on queue depth (Functions/Container Apps)
- Shed load on non-critical endpoints when dependencies degrade
- **Graceful degradation**: Return cached/stale data when dependencies fail; circuit breaker fallbacks

### Chaos-lite

- Regularly test failover of dependencies (simulate downstream outage)

> **Troubleshooting**: For transport-layer troubleshooting (timeouts, connection pooling, retries), see [OSI Model](../software/osi.md).

---

## 7. Reference Blueprints

### A) Global API (multi-region active-active)

- CDN/WAF → API Gateway (regional) → App Service/Container Apps (multi-AZ)
- **Data**: Multi-region database or SQL w/ geo-replication
- **Events**: Regional event service + message bus (geo-dr / paired region)
- **Observability**: Central workspace + distributed tracing across regions

### B) Event-driven Enterprise Integration

- Producers → Message bus topics (sessions by entity/tenant) → consumers
- Outbox publisher for DB consistency
- DLQs + re-drive + replay tooling + incident dashboards

### C) Data Freshness Pipeline

- CDC/Events → Stream ingest → processing (Functions/Container Apps)
- Lake updates + serving store + freshness metrics + alerting

---

> **Note**: For infrastructure deployment, container orchestration, CI/CD, and operational practices, see [Cloud Infrastructure & Operations](infrastructure.md).
