# OSI Model — Troubleshooting Lens (Cloud Emphasis)

**Scope**: Troubleshooting and diagnostic reference using OSI model framework for network and cloud infrastructure issues.

**Purpose**: When things break, use this to systematically diagnose issues by OSI layer (security, connectivity, performance). Applies to general networking and cloud architectures, with emphasis on cloud-specific patterns and controls.

## Table of Contents

- [OSI Model Overview](#osi-model-overview)
- [Layer 1 — Physical (Bits)](#layer-1--physical-bits)
- [Layer 2 — Data Link (Frames)](#layer-2--data-link-frames)
- [Layer 3 — Network (IP, Routing)](#layer-3--network-ip-routing)
- [Layer 4 — Transport (TCP/UDP, Ports, Reliability)](#layer-4--transport-tcpudp-ports-reliability)
- [Layer 5 — Session (Connection Semantics)](#layer-5--session-connection-semantics)
- [Layer 6 — Presentation (Serialization, Encoding)](#layer-6--presentation-serialization-encoding)
- [Layer 7 — Application (Business Protocols + Cloud Services)](#layer-7--application-business-protocols--cloud-services)
- [Cross-Layer Best Practices](#cross-layer-best-practices)
- [Common "Where does this live in OSI?" Mapping](#common-where-does-this-live-in-osi-mapping)

---

## OSI Model Overview

The OSI (Open Systems Interconnection) model is a 7-layer framework for understanding network communication. Each layer has specific responsibilities and protocols.

**General OSI Layers**:
1. **Physical** - Hardware, cables, electrical signals
2. **Data Link** - Frames, MAC addresses, error detection
3. **Network** - IP addresses, routing, packet forwarding
4. **Transport** - TCP/UDP, ports, reliability, flow control
5. **Session** - Connection establishment, management, termination
6. **Presentation** - Data encoding, encryption, compression
7. **Application** - User-facing protocols (HTTP, SMTP, FTP)

**Cloud Context**: In cloud architectures, lower layers (1-3) are often abstracted, while upper layers (4-7) require explicit configuration and troubleshooting.

---

## Layer 1 — Physical (Bits)

### General OSI Definition

- Physical transmission of raw bits over media (cables, fiber, wireless)
- Hardware specifications, electrical signals, physical topology
- Examples: Ethernet cables, network interface cards (NICs), hubs, repeaters

### Cloud Context

- Physical infrastructure is abstracted by cloud providers
- You don't manage hardware, cables, RF, or physical switching
- Physical separation handled via regions, availability zones, data centers

### Cloud Implications

- Choose zone-redundant services where supported
- Use multi-region DR where business requires
- Plan for region/zone physical separation in architecture

### Troubleshooting

- **General**: Check cable connections, NIC status, physical link indicators
- **Cloud**: Most "physical" issues show up as regional incidents/availability events
- Check cloud provider status pages for regional outages

---

## Layer 2 — Data Link (Frames)

### General OSI Definition

- Frame-based communication between directly connected nodes
- MAC addresses, error detection/correction, flow control
- Examples: Ethernet frames, switches, bridges, VLANs
- Protocols: Ethernet, PPP, Frame Relay

### Cloud Context

- Underlying NIC/VLAN/L2 switching abstracted by cloud providers
- You mostly see it via virtual network constructs (VNets, subnets)
- L2 adjacency assumptions don't apply in cloud networking

### Cloud Best Practices

- Prefer managed networking; avoid assumptions about L2 adjacency
- Use proper subnet segmentation in VNets (even for cloud private endpoints)
- Understand that cloud networking is L3-based, not L2-based

### Common Cloud Controls

- **Azure**: NSGs (stateful L4 filtering at subnet/NIC), VNet peering
- **AWS**: Security Groups, VPC peering
- **GCP**: VPC firewall rules, VPC peering

### Troubleshooting

- **General**: Check switch port status, VLAN configuration, MAC address tables
- **Cloud**: If private endpoint traffic fails, check subnet/NSG + private DNS linkage first
- Verify virtual network segmentation and firewall rules

---

## Layer 3 — Network (IP, Routing)

### General OSI Definition

- Logical addressing and routing between networks
- IP addresses, routing protocols, packet forwarding
- Examples: IPv4/IPv6, routers, routing tables, ICMP
- Protocols: IP, ICMP, ARP, routing protocols (OSPF, BGP)

### Cloud Context

- Primary layer you control in cloud designs
- IP addressability, routing boundaries, private/public access posture
- Virtual networks, subnets, route tables, network gateways

### Troubleshooting Focus

- **General**: Check routing tables, IP configuration, gateway connectivity, ping/traceroute
- **Cloud**: 
  - Check if private access is configured (Private Link, Private DNS zones)
  - Verify routing configuration (hub-spoke, Virtual WAN, route tables)
  - Validate egress controls (NAT Gateway, Azure Firewall rules)
  - DNS resolves to public endpoint when you expected private
  - Route tables / peering / firewall blocks expected flows

> **Design patterns**: For networking design patterns, see [Architecture - Networking](cloud_architecture.md#2-networking--edge-advanced).

### Key Checks

- Effective routes, private DNS zones, "public network access" toggles
- IP address ranges, subnet configuration, routing table entries

---

## Layer 4 — Transport (TCP/UDP, Ports, Reliability)

### General OSI Definition

- End-to-end communication, reliability, flow control, error recovery
- Port numbers, segmentation, sequencing, acknowledgments
- Protocols: TCP (reliable, connection-oriented), UDP (unreliable, connectionless)
- Examples: TCP three-way handshake, UDP datagrams, port numbers (0-65535)

### Cloud Context

- Most cloud integration is TCP-based (HTTPS, AMQP, database protocols)
- UDP less common (some DNS scenarios, streaming)
- Connection management critical in cloud (timeouts, pooling, retries)

### Troubleshooting Focus

- **General**: Check port availability, firewall rules, TCP connection state, packet loss
- **Cloud**:
  - Check timeout configurations (HTTP client, DB drivers, messaging clients)
  - Verify retry policies (backoff, jitter, retry budgets)
  - Inspect circuit breaker state and connection pool usage
  - Validate keep-alive and connection pooling settings
  - Hanging calls (missing timeouts), connection exhaustion (no pooling), port blocks

> **Design patterns**: For reliability design patterns, see [Architecture - Reliability](cloud_architecture.md#7-reliability-engineering-sre-level).

### Cloud Examples

- **Azure**: Service Bus (AMQP over TCP), SQL (TDS over TCP), HTTPS everywhere
- **General**: HTTP/HTTPS (TCP 80/443), SMTP (TCP 25), DNS (UDP 53)

### Key Checks

- Client timeouts, connection pool sizes, SNAT/egress capacity (NAT)
- Port availability, TCP connection state, firewall rules

---

## Layer 5 — Session (Connection Semantics)

### General OSI Definition

- Session establishment, management, and termination
- Dialog control, synchronization, checkpointing
- Examples: RPC sessions, SQL sessions, NetBIOS sessions
- Note: In modern networking, often combined with Layer 6/7

### Modern Mapping

- TLS session establishment and management
- Authenticated sessions (login sessions, API sessions)
- Long-lived connections (AMQP, WebSockets, database connections)

### Cloud Context

- Critical for authentication, authorization, and connection management
- Token-based authentication (OAuth, JWT, Managed Identity)
- Session affinity for stateful services

### Troubleshooting Focus

- **General**: Check session establishment, session timeout, connection state
- **Cloud**:
  - Verify TLS configuration and certificate validity/expiry
  - Check token refresh logic and Managed Identity token acquisition
  - Inspect session affinity issues and stateless service assumptions
  - Token expiry mishandled, re-auth loops, broken session affinity

> **Design patterns**: For identity/auth design patterns, see [Architecture - Identity & Auth](cloud_architecture.md#1-identity--auth-advanced).

### Cloud Examples

- **Azure**: Managed Identity token acquisition; Service Bus sessions (ordering semantics)
- **General**: TLS handshake, OAuth token refresh, database connection sessions

### Key Checks

- Token refresh, clock skew, TLS handshake errors, session ordering keys
- Session timeout settings, connection state, authentication failures

---

## Layer 6 — Presentation (Serialization, Encoding)

### General OSI Definition

- Data translation, encryption, compression, formatting
- Character encoding, data format conversion
- Examples: ASCII/UTF-8 encoding, JPEG/PNG compression, encryption/decryption
- Note: Often handled by application layer in modern protocols

### Modern Mapping

- Data serialization formats: JSON, Avro, Protobuf, XML, MessagePack
- Compression: gzip, deflate, brotli
- Character encodings: UTF-8, ASCII, ISO-8859-1
- Schema compatibility and versioning

### Cloud Context

- Critical for API contracts, event schemas, data integration
- Schema evolution and backward compatibility essential
- Encoding issues common in multi-system integrations

### Best Practices

- **Standardize encodings**: UTF-8, ISO timestamps, explicit decimals handling
- **Validate schemas at boundaries**; reject/route bad payloads to quarantine
- **Version payloads** (backward compatible by default)

### Troubleshooting

- **General**: Check character encoding, data format compatibility, compression settings
- **Cloud**: 
  - Breaking schema changes, timezone/format drift, encoding issues, float precision loss
  - Schema version mismatches, serialization errors, encoding conversion failures

### Cloud Examples

- **Azure**: JSON APIs via APIM; XML/SOAP legacy; event payload contracts for Event Grid/Service Bus
- **General**: JSON APIs, XML/SOAP services, binary protocols

### Key Checks

- Schema version, contract tests, serialization settings, date parsing rules
- Character encoding, compression settings, data format validation

---

## Layer 7 — Application (Business Protocols + Cloud Services)

### General OSI Definition

- User-facing protocols and application services
- HTTP, SMTP, FTP, DNS, SNMP, Telnet
- Application-specific data formats and semantics
- Examples: Web browsers, email clients, file transfer, remote login

### Cloud Context

- Where most cloud architecture lives
- API design, integration patterns, messaging, data access, observability, authorization
- REST APIs, message queues, event streams, database protocols

### Troubleshooting Focus

- **General**: Check application errors, protocol violations, authentication failures
- **Cloud**:
  - **API gateway**: Check auth failures, throttling limits, routing issues
  - **Messaging**: Inspect DLQ counts, idempotency key handling, replay failures
  - **Data**: Verify data access patterns, query performance, contract violations
  - **Observability**: Trace correlation IDs, check distributed tracing gaps, validate SLO metrics

> **Design patterns**: For application layer design patterns, see [Architecture](cloud_architecture.md) - Sections 3-4 (APIs, Messaging), Section 7 (Reliability).

### Cloud Examples

- **Azure**: APIM policies, Functions handlers, Service Bus topics, Event Grid fan-out
- **General**: HTTP/HTTPS APIs, message queues (RabbitMQ, Kafka), database protocols (SQL, NoSQL)

### Common Failure Modes

- Duplicate processing, poison messages, unbounded fan-out, "chatty" APIs, N+1 DB calls
- Protocol violations, authentication failures, rate limiting, service unavailability

### Key Checks

- Idempotency keys, DLQ counts, queue depth, error budget burn, dependency latency
- HTTP status codes, API response times, error logs, service health endpoints

---

## Cross-Layer Best Practices

### Security Posture

- **L3**: Prefer private endpoints for data plane + private DNS (cloud); use VPNs/firewalls (general)
- **L4**: TLS everywhere; restrict ports; timeouts + retries
- **L5**: Secure session establishment, certificate validation
- **L7**: Central auth at gateway + app-level authorization checks

### Reliability

- **L1/L3**: Zone redundancy + DR tier defined (cloud); redundant paths (general)
- **L4**: Bounded concurrency, connection pooling, backpressure, retry policies
- **L7**: Idempotent consumers + DLQ + replay, circuit breakers

### Troubleshooting Workflow (Top-Down)

1. **L7**: Is the app returning errors? Check logs/traces, HTTP status codes, error messages
2. **L6**: Payload/schema/encoding issues? Validate contract + sample message, check serialization
3. **L5/L4**: Auth/session/timeouts/retries? Inspect tokens + client settings, connection state
4. **L3**: DNS/private endpoint/routing? Verify name resolution + effective routes, IP connectivity
5. **L2**: Firewall rules/peering? Verify allowed flows, network segmentation
6. **L1**: Physical connectivity? Check cables, NIC status (general); region incident? Check platform health/status (cloud)

---

## Common "Where does this live in OSI?" Mapping

### General Networking
- **DNS** → Layer 3 (name→IP reachability) or Layer 7 (application protocol)
- **Routers / routing protocols** → Layer 3
- **Switches / bridges** → Layer 2
- **Firewalls** → Layer 3/4 (packet filtering) or Layer 7 (application filtering)
- **Load balancers** → Layer 4 (TCP/UDP) or Layer 7 (HTTP/HTTPS)
- **TLS / certificates** → Layer 5/6 (secure session + presentation)
- **Ports / TCP keepalive / pooling** → Layer 4
- **HTTP / HTTPS** → Layer 7
- **SMTP / FTP** → Layer 7

### Cloud-Specific
- **WAF / API Gateway policies** → Layer 7
- **JWT / OAuth tokens** → Layer 5–7 (session/auth semantics)
- **Private Link / private endpoints** → Layer 3/4
- **Private DNS zones** → Layer 3 (name→IP reachability)
- **JSON/XML/Avro schema evolution** → Layer 6
- **Messaging semantics** (idempotency, DLQ) → Layer 7 (application protocol behavior)
- **Managed Identity / service principals** → Layer 5–7 (authentication)

---

> **Note**: If you tell me your target stack (e.g., Front Door + APIM + Functions + Service Bus + SQL), I can produce an OSI-layer "troubleshooting playbook" tailored to that architecture.

