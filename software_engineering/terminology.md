# Programming Terminology

**Related**: [Resources](resources.md) | [Concepts](concepts/)

---

## Software Architecture & Design

- **API Versioning**: Managing multiple API versions for backward compatibility
- **Bounded Context**: Explicit boundary where domain model applies (DDD concept)
- **Bulkhead Pattern**: Isolating components to prevent cascading failures
- **CQRS (Command Query Responsibility Segregation)**: Separating read and write operations
- **DDD (Domain-Driven Design)**: Software design approach focusing on domain model
- **Design Pattern**: Reusable solution to common software design problems
- **Event Sourcing**: Storing state changes as sequence of events
- **Hexagonal Architecture**: Architecture isolating business logic from infrastructure
- **Microservices**: Architecture pattern with independently deployable services
- **Monolith**: Single deployable unit containing all application functionality
- **Outbox Pattern**: Reliable event publishing using database transactions
- **Retry Pattern**: Automatically retrying failed operations with backoff
- **Saga Pattern**: Managing distributed transactions across services using compensating actions
- **SOLID Principles**: Single responsibility, Open-closed, Liskov substitution, Interface segregation, Dependency inversion

---

## Data Structures & Algorithms

- **Array**: Contiguous memory storing elements of same type; O(1) access by index
- **BFS (Breadth-First Search)**: Graph traversal exploring neighbors before children
- **Big O Notation**: Describes algorithm time/space complexity as input grows
- **Binary Search**: Finding element in sorted array by halving search space; O(log n)
- **Binary Tree**: Tree where each node has at most two children
- **DFS (Depth-First Search)**: Graph traversal exploring as deep as possible before backtracking
- **Dynamic Programming**: Solving problems by breaking into overlapping subproblems; memoization
- **Graph**: Data structure with nodes (vertices) and edges; models relationships
- **Greedy Algorithm**: Making locally optimal choices at each step
- **Hash Table/HashMap**: Key-value store with O(1) average lookup using hash function
- **Heap**: Tree-based structure where parent is greater/less than children; priority queue
- **Linked List**: Linear structure where elements point to next; O(1) insert, O(n) access
- **Memoization**: Caching function results to avoid redundant computation
- **Queue**: FIFO (First In, First Out) data structure
- **Recursion**: Function calling itself to solve smaller subproblems
- **Sliding Window**: Technique for processing subarrays/substrings efficiently
- **Space Complexity**: Memory usage of algorithm as function of input size
- **Stack**: LIFO (Last In, First Out) data structure
- **Time Complexity**: Execution time of algorithm as function of input size
- **Tree**: Hierarchical structure with root node and children; no cycles
- **Trie**: Tree for storing strings; enables prefix search
- **Two Pointers**: Technique using two indices to traverse array efficiently

---

## DevOps & Operations

- **Blue-Green Deployment**: Deployment strategy with two identical environments
- **Canary Deployment**: Gradual rollout to subset of users before full release
- **Configuration Drift**: Unplanned divergence between infrastructure state and definition
- **Feature Flag**: Toggle enabling/disabling features without code deployment
- **MTBF (Mean Time Between Failures)**: Average time between consecutive failures
- **MTTD (Mean Time To Detection)**: Average time to detect a failure or incident
- **MTTF (Mean Time To Failure)**: Average time until first failure
- **MTTR (Mean Time To Recovery)**: Average time to recover from failure
- **Observability**: Ability to understand system state from external outputs (logs, metrics, traces)
- **Platform Engineering**: Practice of building internal developer platforms and tooling
- **Runbook**: Documented procedures for routine operations and incident response
- **Shift Left**: Moving testing, security, and quality earlier in development lifecycle
- **Shift Right**: Testing and monitoring in production environments
- **SRE (Site Reliability Engineering)**: Practice applying software engineering to operations problems
- **Toil**: Repetitive, manual, automatable work that scales with service growth

---

## Security & Compliance

- **CSPM (Cloud Security Posture Management)**: Continuous monitoring of cloud security configuration
- **CWPP (Cloud Workload Protection Platform)**: Security platform protecting cloud workloads
- **DAST (Dynamic Application Security Testing)**: Testing running applications for vulnerabilities
- **Least Privilege**: Granting minimum permissions necessary to perform a function
- **SAST (Static Application Security Testing)**: Analyzing source code for security vulnerabilities
- **SBOM (Software Bill of Materials)**: Inventory of components and dependencies in software
- **SCA (Software Composition Analysis)**: Identifying vulnerabilities in open-source dependencies
- **SIEM (Security Information and Event Management)**: Centralized security event monitoring
- **SOAR (Security Orchestration, Automation and Response)**: Automating security operations
- **Supply Chain Security**: Protecting software supply chain from vulnerabilities and attacks
- **Threat Modeling**: Identifying potential security threats and mitigations
