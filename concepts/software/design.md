# Design Patterns & Best Practices

**Scope**: Enterprise software design patterns, architectural principles, and best practices for building scalable, maintainable systems.

**Purpose**: Design patterns and principles for enterprise software development, independent of specific cloud platforms.

## Table of Contents

- [1. Architectural Principles](#1-architectural-principles)
- [2. Domain-Driven Design (DDD)](#2-domain-driven-design-ddd)
- [3. Microservices Patterns](#3-microservices-patterns)
- [4. API Design Patterns](#4-api-design-patterns)
- [5. Event-Driven Architecture](#5-event-driven-architecture)
- [6. CQRS & Event Sourcing](#6-cqrs--event-sourcing)
- [7. Distributed Systems Patterns](#7-distributed-systems-patterns)
- [8. Security Patterns](#8-security-patterns)
- [9. Observability & Monitoring](#9-observability--monitoring)
- [10. Testing Strategies](#10-testing-strategies)

---

## 1. Architectural Principles

### SOLID Principles

- **Single Responsibility**: Each class/module has one reason to change
- **Open/Closed**: Open for extension, closed for modification
- **Liskov Substitution**: Subtypes must be substitutable for base types
- **Interface Segregation**: Many specific interfaces > one general interface
- **Dependency Inversion**: Depend on abstractions, not concretions

### Design Principles

- **Separation of Concerns**: Business logic, data access, presentation separate
- **DRY (Don't Repeat Yourself)**: Single source of truth for logic/data
- **KISS (Keep It Simple, Stupid)**: Prefer simple solutions over complex
- **YAGNI (You Aren't Gonna Need It)**: Don't build for hypothetical future needs
- **Fail Fast**: Detect errors early, fail immediately with clear messages

### Architectural Styles

- **Layered Architecture**: Presentation → Business → Data layers
- **Hexagonal Architecture**: Ports and adapters, business logic at center
- **Clean Architecture**: Dependency rule: dependencies point inward
- **Onion Architecture**: Core domain → application → infrastructure layers

---

## 2. Domain-Driven Design (DDD)

### Strategic Design

- **Bounded Context**: Explicit boundaries where domain model applies
- **Ubiquitous Language**: Shared vocabulary between domain experts and developers
- **Context Mapping**: Relationships between bounded contexts (shared kernel, customer/supplier, conformist, etc.)

### Tactical Design

- **Entities**: Objects with identity (User, Order)
- **Value Objects**: Immutable objects defined by attributes (Money, Address)
- **Aggregates**: Cluster of entities treated as single unit (Order + OrderItems)
- **Aggregate Root**: Single entry point to aggregate (Order)
- **Domain Events**: Something that happened in the domain (OrderPlaced, PaymentReceived)
- **Repositories**: Abstraction for aggregate persistence
- **Domain Services**: Operations that don't belong to single entity
- **Factories**: Encapsulate complex object creation

### Patterns

- **Specification Pattern**: Encapsulate business rules as reusable predicates
- **Strategy Pattern**: Encapsulate algorithms, make them interchangeable
- **Factory Pattern**: Centralize object creation logic

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

## 10. Testing Strategies

### Testing Pyramid

- **Unit Tests**: Fast, isolated, test single functions/classes
- **Integration Tests**: Test interactions between components
- **Contract Tests**: Verify API contracts between services
- **End-to-End Tests**: Test complete user workflows (few, slow)

### Testing Patterns

- **Test-Driven Development (TDD)**: Write tests before code
- **Behavior-Driven Development (BDD)**: Tests in natural language
- **Mocking**: Replace dependencies with test doubles
- **Test Fixtures**: Reusable test data setup
- **Property-Based Testing**: Generate test cases automatically

### Test Types

- **Unit Tests**: Fast, test business logic in isolation
- **Integration Tests**: Test database, external APIs, message queues
- **Contract Tests**: Verify API contracts (Pact, Spring Cloud Contract)
- **Chaos Engineering**: Test system resilience to failures

---

> **Note**: These patterns are platform-agnostic. For cloud-specific implementations, see [Architecture](cloud_architecture.md).

