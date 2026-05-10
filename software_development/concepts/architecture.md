# Software Architecture Patterns

**Scope**: System-level architectural patterns, service design, integration patterns, and distributed systems best practices.

**Purpose**: Use this when designing system architecture, service boundaries, integration patterns, and distributed systems. For code-level design patterns, see [Software Design](design.md).

## Table of Contents

- [1. Architectural Styles](#1-architectural-styles)
- [2. Domain-Driven Design (Strategic)](#2-domain-driven-design-strategic)
- [3. Microservices Patterns](#3-microservices-patterns)
- [4. API Design Patterns](#4-api-design-patterns)
- [5. Event-Driven Architecture](#5-event-driven-architecture)
- [6. CQRS & Event Sourcing](#6-cqrs--event-sourcing)
- [7. Distributed Systems Patterns](#7-distributed-systems-patterns)
- [8. Security Patterns](#8-security-patterns)
- [9. Observability & Monitoring](#9-observability--monitoring)

---

## 1. Architectural Styles

### Layered Architecture

- **Presentation Layer**: User interface, API endpoints
- **Business Layer**: Business logic, domain rules
- **Data Layer**: Data access, persistence
- **Pattern**: Dependencies flow downward; each layer depends only on layers below
- **Use case**: Traditional enterprise applications, monoliths

### Hexagonal Architecture (Ports & Adapters)

- **Core**: Business logic at center, independent of external concerns
- **Ports**: Interfaces defining interactions (input/output ports)
- **Adapters**: Implementations of ports (REST, database, messaging)
- **Pattern**: Business logic isolated from infrastructure
- **Use case**: Applications with multiple interfaces, testability

### Clean Architecture

- **Dependency Rule**: Dependencies point inward (outer layers depend on inner)
- **Layers**: Entities → Use Cases → Interface Adapters → Frameworks
- **Independence**: Business logic independent of frameworks, UI, databases
- **Testability**: Easy to test business logic in isolation
- **Use case**: Long-lived applications, complex business logic

### Onion Architecture

- **Core Domain**: Innermost layer, pure business logic
- **Application Services**: Use cases, orchestration
- **Domain Services**: Cross-cutting domain logic
- **Infrastructure**: External concerns (databases, APIs, frameworks)
- **Pattern**: Dependencies point inward; core has no dependencies

---

## 2. Domain-Driven Design (Strategic)

### Bounded Context

- **Definition**: Explicit boundary where domain model applies
- **Purpose**: Isolate domain models, prevent model pollution
- **Context per Team**: Each team owns one or more bounded contexts
- **Ubiquitous Language**: Shared vocabulary within bounded context

### Context Mapping

- **Shared Kernel**: Shared code between contexts (use sparingly)
- **Customer/Supplier**: Upstream context serves downstream
- **Conformist**: Downstream conforms to upstream model
- **Anti-Corruption Layer**: Translate between contexts, protect domain model
- **Separate Ways**: Independent contexts, no integration
- **Partnership**: Mutual dependency between contexts
- **Published Language**: Well-defined interface between contexts

### Strategic Patterns

- **Domain Events**: Cross-context communication via events
- **Distributed Bounded Context**: Context spans multiple services
- **Event-Driven Communication**: Decouple contexts via events

---

## 3. Microservices Patterns

### Service Decomposition

- **Decompose by Business Capability**: Align services with business functions
- **Decompose by Subdomain**: Based on DDD bounded contexts
- **Database per Service**: Each service owns its database
- **API Gateway**: Single entry point for clients, routes to services

### Communication Patterns

- **Synchronous**: REST, gRPC for request/response
- **Asynchronous**: Messaging, events for decoupled communication
- **Service Mesh**: Infrastructure layer for service-to-service communication
- **Saga Pattern**: Manage distributed transactions across services

### Data Management

- **Database per Service**: Isolation, independent scaling
- **Shared Database Anti-pattern**: Avoid sharing databases between services
- **API Composition**: Aggregate data from multiple services
- **CQRS**: Separate read and write models

### Resilience

- **Circuit Breaker**: Prevent cascading failures
- **Bulkhead**: Isolate failures to specific service pools
- **Timeout**: Prevent indefinite waiting
- **Retry with Backoff**: Handle transient failures
- **Health Checks**: Monitor service availability

---

## 4. API Design Patterns

### RESTful Design

- **Resource-Based URLs**: `/users/{id}`, `/orders/{id}/items`
- **HTTP Methods**: GET (read), POST (create), PUT (replace), PATCH (partial update), DELETE (remove)
- **Status Codes**: 200 (OK), 201 (Created), 400 (Bad Request), 404 (Not Found), 500 (Server Error)
- **HATEOAS**: Hypermedia as the engine of application state
- **Pagination**: Limit, offset, or cursor-based
- **Filtering & Sorting**: Query parameters for flexible queries

### API Versioning

- **URL Versioning**: `/v1/users`, `/v2/users`
- **Header Versioning**: `Accept: application/vnd.api+json;version=2`
- **Query Parameter**: `/users?version=2`
- **Backward Compatibility**: Add fields, don't remove; deprecate before removing

### API Patterns

- **API Gateway**: Single entry point, routing, auth, rate limiting
- **Backend for Frontend (BFF)**: Separate API per client type
- **GraphQL**: Query language for APIs, client specifies data needs
- **gRPC**: High-performance RPC framework, protocol buffers

---

## 5. Event-Driven Architecture

### Event Patterns

- **Event Sourcing**: Store events, not current state; rebuild state from events
- **Event Streaming**: Continuous flow of events (Kafka, Event Hubs)
- **Pub/Sub**: Publishers emit events, subscribers consume
- **Event Carrying State Transfer**: Events contain full state, not just deltas

### Event Design

- **Event Naming**: Past tense verbs (OrderPlaced, PaymentReceived)
- **Event Schema**: Versioned, backward-compatible schemas
- **Idempotency**: Process same event multiple times safely
- **Event Ordering**: Handle out-of-order events (watermarks, timestamps)

### Patterns

- **Event-Driven Saga**: Coordinate distributed transactions via events
- **CQRS**: Separate command (write) and query (read) models
- **Event Replay**: Reprocess events for debugging, migration, analytics

---

## 6. CQRS & Event Sourcing

### CQRS (Command Query Responsibility Segregation)

- **Separate Models**: Different models for reads and writes
- **Command Side**: Optimized for writes, business logic, validation
- **Query Side**: Optimized for reads, denormalized views, fast queries
- **Event Bus**: Synchronize read models from write model events

### Event Sourcing

- **Event Store**: Append-only log of events
- **State Reconstruction**: Replay events to rebuild current state
- **Snapshots**: Periodic snapshots to avoid replaying all events
- **Projections**: Build read models from events

### Benefits

- **Audit Trail**: Complete history of changes
- **Time Travel**: Reconstruct state at any point in time
- **Debugging**: Understand why state changed
- **Scalability**: Separate read/write scaling

---

## 7. Distributed Systems Patterns

### Consistency Patterns

- **Strong Consistency**: All nodes see same data immediately (ACID)
- **Eventual Consistency**: Nodes converge to same state over time (BASE)
- **CAP Theorem**: Choose 2 of 3: Consistency, Availability, Partition tolerance
- **Consensus Algorithms**: Raft, Paxos for distributed agreement

### Coordination Patterns

- **Distributed Lock**: Coordinate access to shared resources
- **Leader Election**: Select single leader from group
- **Two-Phase Commit**: Distributed transaction protocol
- **Saga Pattern**: Long-running transaction coordinator

### Resilience Patterns

- **Circuit Breaker**: Stop calling failing service, fail fast
- **Bulkhead**: Isolate resources to prevent cascading failures
- **Retry**: Handle transient failures with exponential backoff
- **Timeout**: Prevent indefinite blocking
- **Rate Limiting**: Control request rate per client/service

---

## 8. Security Patterns

### Authentication & Authorization

- **OAuth 2.0**: Authorization framework, access tokens
- **OpenID Connect (OIDC)**: Identity layer on OAuth 2.0
- **JWT (JSON Web Tokens)**: Stateless authentication tokens
- **RBAC (Role-Based Access Control)**: Permissions via roles
- **ABAC (Attribute-Based Access Control)**: Permissions via attributes

### Security Patterns

- **Defense in Depth**: Multiple security layers
- **Least Privilege**: Minimum permissions required
- **Zero Trust**: Never trust, always verify
- **Secure by Default**: Secure configuration out of the box
- **Fail Secure**: Default to deny on failure

### Data Protection

- **Encryption at Rest**: Encrypt stored data
- **Encryption in Transit**: TLS/SSL for network communication
- **Secrets Management**: Secure storage and rotation of secrets
- **Data Masking**: Hide sensitive data in non-production
- **Audit Logging**: Track access and changes

---

## 9. Observability & Monitoring

### Three Pillars

- **Metrics**: Numerical measurements over time (CPU, latency, error rate)
- **Logs**: Discrete events with timestamps and context
- **Traces**: Request flow across distributed systems

### Observability Patterns

- **Distributed Tracing**: Track requests across services (OpenTelemetry, Zipkin)
- **Structured Logging**: JSON logs with consistent fields
- **Correlation IDs**: Track requests across services
- **Health Checks**: Liveness and readiness probes
- **SLI/SLO/SLA**: Service level indicators, objectives, agreements

### Best Practices

- **Log Levels**: DEBUG, INFO, WARN, ERROR, FATAL
- **Context Enrichment**: Add request ID, user ID, tenant ID to logs
- **Sampling**: Sample high-volume traces/logs
- **Alerting**: Alert on SLO violations, not raw metrics

---

> **Note**: For code-level design patterns and principles, see [Software Design](design.md). For cloud-specific implementations, see [Cloud Architecture](../../data_management/concepts/platform/architecture.md).

