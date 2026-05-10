# Data Management Terminology

**Related**: [Standards](../_resources/standards.md) · [Tools](../_resources/tools.md) | [Concepts](concepts/) ([core](concepts/core/) · [platform](concepts/platform/))

---

## Cloud & Infrastructure

- **API Gateway**: Centralized entry point for API requests; handles routing, authentication, rate limiting
- **CDN (Content Delivery Network)**: Distributed network for delivering content with low latency
- **CI/CD (Continuous Integration/Deployment)**: Automated build, test, and deployment pipelines
- **Circuit Breaker**: Pattern to prevent cascading failures by stopping requests to failing services
- **Container Orchestration**: Managing containerized applications (Kubernetes, Docker Swarm)
- **DDoS Protection**: Distributed Denial of Service protection at network edge
- **DNS (Domain Name System)**: System translating domain names to IP addresses
- **DR (Disaster Recovery)**: Strategy and processes for recovering IT systems after catastrophic failure
- **Event-Driven Architecture**: Architecture pattern using events for communication between services
- **GitOps**: Operational model using Git as source of truth for infrastructure
- **HA (High Availability)**: System design ensuring continuous operation with minimal downtime
- **Horizontal Scaling**: Adding more instances/nodes to handle increased load (scale out)
- **IaC (Infrastructure as Code)**: Managing infrastructure through code/configuration (Terraform, Bicep)
- **Immutable Infrastructure**: Infrastructure replaced rather than modified; ensures consistency
- **Kubernetes**: Container orchestration platform for automated deployment and scaling
- **Load Balancer**: Distributes traffic across multiple servers
- **Managed Identity**: Cloud-native identity for resources (no secrets required)
- **mTLS (Mutual TLS)**: Mutual authentication using TLS certificates
- **Multi-tenant SaaS**: Software architecture serving multiple customers from single instance
- **NAT (Network Address Translation)**: Translating private IPs to public IPs
- **OIDC (OpenID Connect)**: Authentication protocol built on OAuth 2.0
- **Private Link**: Secure connectivity to cloud services without public internet
- **Rolling Deployment**: Gradual replacement of instances with new versions (zero downtime)
- **RPO (Recovery Point Objective)**: Maximum acceptable data loss measured in time
- **RTO (Recovery Time Objective)**: Maximum acceptable downtime after failure
- **Serverless**: Cloud computing model where provider manages infrastructure
- **Service Mesh**: Infrastructure layer for managing service-to-service communication
- **Vertical Scaling**: Increasing resources (CPU, RAM) on existing instance (scale up)
- **VNet (Virtual Network)**: Isolated network environment in cloud
- **WAF (Web Application Firewall)**: Firewall protecting web applications from attacks
- **Zero Trust**: Security model requiring verification for every access request

---

## Networking & Protocols

- **Bandwidth**: Maximum data transfer capacity of a network connection
- **CORS (Cross-Origin Resource Sharing)**: HTTP mechanism allowing cross-domain requests
- **Firewall**: Network security system controlling incoming/outgoing traffic
- **GraphQL**: Query language for APIs; client specifies exact data needed
- **HTTP/HTTPS**: Hypertext Transfer Protocol; HTTPS adds TLS encryption
- **IP Address**: Unique numerical identifier for devices on a network
- **Latency**: Time delay between request and response
- **Load Balancer**: Distributes traffic across multiple servers for availability
- **OSI Model**: Seven-layer networking model (Physical, Data Link, Network, Transport, Session, Presentation, Application)
- **Proxy**: Intermediary server between client and destination server
- **Rate Limiting**: Controlling number of requests a client can make in a time period
- **REST (Representational State Transfer)**: Architectural style for web APIs using HTTP methods
- **Reverse Proxy**: Server that forwards client requests to backend servers (nginx, HAProxy)
- **Subnet**: Logical subdivision of an IP network
- **TCP (Transmission Control Protocol)**: Reliable, ordered, connection-oriented protocol
- **Throughput**: Actual data transferred per unit of time
- **UDP (User Datagram Protocol)**: Fast, connectionless protocol; no delivery guarantee
- **WebSocket**: Protocol for full-duplex, persistent connections between client and server

---

## Azure & Microsoft Data Platform

- **ADF (Azure Data Factory)**: Cloud-based data integration and orchestration service
- **ADLS Gen2 (Azure Data Lake Storage)**: Scalable data lake storage with hierarchical namespace
- **Delta Lake**: Open-source storage layer with ACID transactions for data lakes
- **Delta Live Tables (DLT)**: Databricks declarative framework for building data pipelines
- **Direct Lake**: Power BI mode querying OneLake Delta tables directly (no import/DirectQuery)
- **Medallion Architecture**: Bronze (raw) → Silver (cleaned) → Gold (curated) data layers
- **OneLake**: Microsoft Fabric unified data lake storage across workspaces
- **Parquet**: Columnar storage format optimized for analytics workloads
- **Photon Engine**: Databricks accelerated SQL engine for faster query performance
- **Real-Time Intelligence**: Fabric workload for streaming data processing
- **Shortcuts**: Fabric references to external data without copying
- **Structured Streaming**: Spark API for stream processing with exactly-once semantics
- **Synapse Analytics**: Analytics service combining data warehousing and big data
- **Unity Catalog**: Databricks unified governance layer for data assets
- **Workspace**: Databricks/Fabric environment for notebooks, jobs, clusters

---

## Data Engineering & Architecture

- **At-Least-Once Delivery**: Message delivered one or more times; requires idempotent consumers
- **Backpressure**: Mechanism to slow producers when consumers can't keep up
- **Batch Processing**: Processing data in scheduled batches (hourly, daily)
- **CDC (Change Data Capture)**: Capturing incremental changes from source systems
- **Data Contract**: Agreement defining schema, semantics, and SLAs for data products
- **Data Drift**: Unexpected changes in data distribution or schema over time
- **Data Fabric**: Architecture pattern providing unified data access across environments
- **Data Lake**: Centralized repository storing raw data in native format
- **Data Lakehouse**: Architecture combining data lake flexibility with warehouse structure
- **Data Lineage**: Tracking data flow from source to consumption
- **Data Mesh**: Decentralized data architecture with domain-oriented ownership
- **Data Product**: Self-contained, reusable data asset with defined contract
- **Data Warehouse**: Centralized repository for structured, queryable data
- **DLQ (Dead Letter Queue)**: Queue for messages that fail processing; enables retry/investigation
- **ELT (Extract, Load, Transform)**: Load raw data first, transform in destination
- **ETL (Extract, Transform, Load)**: Transform data before loading to destination
- **Exactly-Once Delivery**: Message processed exactly once; requires coordination
- **Idempotency**: Operation that produces same result regardless of execution count
- **Incremental Processing**: Processing only new/changed data since last run
- **Lambda Architecture**: Architecture combining batch and streaming processing
- **Metadata**: Data about data (schema, lineage, quality, usage statistics)
- **Orchestration**: Coordinating multiple data pipeline tasks (Airflow, ADF, Prefect)
- **Schema Evolution**: Managing schema changes while maintaining compatibility
- **Schema-on-Read**: Schema applied when reading data (flexible, late binding)
- **Schema-on-Write**: Schema enforced when writing data (strict, early binding)
- **Star Schema**: Data warehouse schema with central fact tables and dimension tables
- **Streaming Processing**: Real-time continuous data processing
- **Watermarking**: Tracking high-water mark for incremental data loads

---

## Data Quality & Operations

- **Data Catalog**: Inventory of data assets with metadata and discovery
- **Data Freshness**: Measure of how current data is (time since last update)
- **Data Observability**: Monitoring data health, quality, and lineage across systems
- **Data Profiling**: Analyzing data to understand structure, content, and quality issues
- **Data Quality**: Measure of data accuracy, completeness, consistency, timeliness
- **Data Reliability Engineering**: Practice of ensuring data systems meet quality and availability targets
- **Data SLO (Service Level Objective)**: Targets for data freshness, completeness, accuracy
- **Data Steward**: Person responsible for data quality and governance within a domain
- **Data Validation**: Checking data against rules, constraints, and expectations
- **Golden Record**: Single authoritative version of an entity across systems
- **MDM (Master Data Management)**: Processes ensuring consistent, accurate master data across systems
- **Quarantine**: Isolating bad/invalid data for review and remediation

---

## Database & Security

- **ACID**: Atomicity, Consistency, Isolation, Durability (transaction properties)
- **CLS (Column-Level Security)**: Restricting access to specific columns
- **Encryption at Rest**: Protecting stored data through encryption
- **Encryption in Transit**: Protecting data during transmission (TLS/SSL)
- **IAM (Identity and Access Management)**: Framework for managing digital identities and access
- **Index**: Database structure improving query performance
- **MFA (Multi-Factor Authentication)**: Authentication requiring multiple verification methods
- **Partitioning**: Dividing large tables into smaller, manageable pieces
- **PII (Personally Identifiable Information)**: Data that can identify individuals
- **Query Optimization**: Improving database query performance through execution plans
- **RBAC (Role-Based Access Control)**: Access permissions based on user roles
- **RLS (Row-Level Security)**: Restricting access to specific rows based on user context
- **Secrets Management**: Secure storage and access control for credentials, keys, tokens
- **SSO (Single Sign-On)**: Authentication allowing access to multiple systems with one login
- **Transaction**: Unit of work that must complete entirely or not at all

---

## AI, ML & Data Science

- **A/B Testing**: Comparing two variants to determine which performs better
- **Agentic AI**: AI systems that can autonomously plan and execute multi-step tasks
- **AI Governance**: Framework for managing AI systems responsibly and ethically
- **Attention Mechanism**: Neural network component focusing on relevant input parts
- **Bias (ML)**: Systematic error from assumptions; underfitting
- **Classification**: Predicting discrete categories (spam/not spam)
- **CNN (Convolutional Neural Network)**: Neural network for image/spatial data processing
- **Confusion Matrix**: Table showing true/false positives and negatives
- **Cross-Validation**: Evaluating model on different data subsets to assess generalization
- **Deep Learning**: Neural networks with multiple layers for complex pattern recognition
- **Embedding**: Vector representation of data (text, images) for ML models
- **Epoch**: One complete pass through entire training dataset
- **Feature Engineering**: Creating/selecting input variables to improve model performance
- **Feature Store**: Centralized repository for ML features; enables reuse and consistency
- **Gradient Descent**: Optimization algorithm minimizing loss by following gradient
- **Grounding**: Connecting AI responses to factual, verifiable information
- **Hallucination**: AI generating plausible but incorrect or fabricated information
- **Hyperparameter Tuning**: Optimizing model configuration (learning rate, layers, etc.)
- **Inference**: Using trained model to make predictions on new data
- **LLM (Large Language Model)**: AI model trained on large text datasets for natural language tasks
- **Loss Function**: Measures difference between predicted and actual values
- **MLOps**: Practices for deploying and maintaining ML models in production
- **Model Drift**: Degradation of model performance over time as data changes
- **Model Fine-tuning**: Adapting pre-trained models for specific tasks or domains
- **Model Registry**: Versioned repository for ML models with metadata
- **Neural Network**: Computing system inspired by biological neurons; learns patterns
- **Overfitting**: Model learns training data too well; poor generalization
- **Precision**: True positives / (True positives + False positives)
- **Prompt Engineering**: Crafting effective prompts to get desired outputs from AI models
- **RAG (Retrieval Augmented Generation)**: AI pattern combining retrieval with generation for accurate responses
- **Recall**: True positives / (True positives + False negatives)
- **Regression**: Predicting continuous values (price, temperature)
- **Supervised Learning**: Training with labeled data (input-output pairs)
- **Token**: Basic unit of text processed by language models
- **Training**: Process of teaching model using data to adjust weights
- **Transformer**: Neural network architecture using self-attention; basis for GPT, BERT
- **Underfitting**: Model too simple to capture underlying patterns
- **Unsupervised Learning**: Training with unlabeled data; finds patterns/clusters
- **Variance (ML)**: Sensitivity to training data fluctuations; overfitting
