# IT Project Management — IT-Specific Concepts

**Scope**: Project management patterns, practices, and strategies specific to IT projects (software, infrastructure, data, cloud, security).

**Purpose**: Use this for IT project management concerns. For general PM fundamentals, see [General Project Management](general.md). For methodology frameworks, see [Project Management Methodologies](methodologies.md).

## Table of Contents

- [1. IT Project Types](#1-it-project-types)
- [2. SDLC Integration](#2-sdlc-integration)
- [3. Technical Debt Management](#3-technical-debt-management)
- [4. Infrastructure Projects](#4-infrastructure-projects)
- [5. Cloud & Data Projects](#5-cloud--data-projects)
- [6. Security & Compliance Projects](#6-security--compliance-projects)
- [7. DevOps Integration](#7-devops-integration)
- [8. Vendor & Third-Party Management](#8-vendor--third-party-management)
- [9. IT-Specific Risks](#9-it-specific-risks)
- [10. IT Project Success Patterns](#10-it-project-success-patterns)
- [11. Enterprise Architecture Alignment](#11-enterprise-architecture-alignment)
- [12. IT Governance Frameworks](#12-it-governance-frameworks)

---

## 1. IT Project Types

### Software Development

- **New product development**: greenfield applications, new features
- **Enhancement projects**: feature additions, improvements to existing systems
- **Maintenance projects**: bug fixes, patches, technical updates
- **Integration projects**: system integrations, API development, data pipelines

### Infrastructure Projects

- **Cloud migrations**: lift-and-shift, re-platform, re-architect
- **Data center projects**: hardware refresh, network upgrades, facility changes
- **Platform upgrades**: OS upgrades, database migrations, middleware updates
- **Security infrastructure**: identity systems, network security, monitoring

### Data Projects

- **Data platform builds**: data lakes, warehouses, analytics platforms
- **Data migrations**: system migrations, data quality initiatives
- **BI/Analytics projects**: reporting, dashboards, data products
- **ML/AI projects**: model development, MLOps, feature engineering

### IT Operations Projects

- **Automation projects**: CI/CD pipelines, infrastructure as code, monitoring
- **Service improvements**: SLA enhancements, performance optimization
- **Disaster recovery**: DR planning, backup systems, failover procedures

---

## 2. SDLC Integration

### SDLC Phases & PM Alignment

- **Requirements**: PM manages scope, stakeholders; tech team defines technical requirements
- **Design**: PM manages timeline, resources; tech team creates architecture/design
- **Development**: PM tracks progress, blockers; tech team builds
- **Testing**: PM ensures quality gates; QA team validates
- **Deployment**: PM coordinates release; ops team executes
- **Operations**: PM transitions to support; ops team maintains

### PM in Agile SDLC

- **Sprint planning**: PM facilitates, ensures business priorities clear
- **Daily standups**: PM observes, removes blockers, tracks progress
- **Sprint review**: PM ensures stakeholder feedback captured
- **Retrospective**: PM facilitates, tracks action items
- **Release planning**: PM coordinates releases, manages dependencies

### Best Practices

- **Technical vs business scope**: PM manages business scope; tech lead manages technical scope
- **Definition of Done**: PM ensures business acceptance criteria; tech ensures technical criteria
- **Dependencies**: PM tracks cross-team dependencies; tech tracks technical dependencies
- **Risk management**: PM manages business risks; tech manages technical risks

---

## 3. Technical Debt Management

### Technical Debt Types

- **Code debt**: shortcuts, poor patterns, lack of tests
- **Architecture debt**: outdated patterns, scalability limits, integration issues
- **Infrastructure debt**: outdated platforms, security gaps, performance bottlenecks
- **Documentation debt**: missing docs, outdated specs, unclear processes

### Managing Technical Debt

- **Debt tracking**: maintain debt backlog, prioritize by impact
- **Debt allocation**: allocate capacity (e.g., 20% per sprint) for debt reduction
- **Debt prevention**: code reviews, architecture reviews, quality gates
- **Debt communication**: explain debt impact to stakeholders, justify refactoring time

### PM Role in Technical Debt

- **Balance**: balance feature delivery with debt reduction
- **Advocacy**: advocate for debt reduction when it blocks features or increases risk
- **Visibility**: make debt visible in roadmaps, track debt metrics
- **Trade-offs**: help stakeholders understand debt vs feature trade-offs

---

## 4. Infrastructure Projects

### Infrastructure Project Characteristics

- **Long lead times**: hardware procurement, vendor coordination, facility changes
- **Dependencies**: infrastructure often blocks application projects
- **Risk**: production impact, downtime windows, rollback complexity
- **Compliance**: regulatory requirements, security standards, audit trails

### Infrastructure Project Patterns

- **Phased rollouts**: pilot → limited → full rollout
- **Parallel systems**: run old and new in parallel, gradual cutover
- **Rollback plans**: always have rollback procedures, test them
- **Change windows**: scheduled maintenance windows, communicate clearly

### Best Practices

- **Early planning**: infrastructure projects need longer planning cycles
- **Vendor coordination**: manage vendor timelines, dependencies, contracts
- **Testing**: test in non-production, validate rollback procedures
- **Communication**: communicate impact, downtime, cutover plans clearly

---

## 5. Cloud & Data Projects

### Cloud Migration Projects

- **Assessment phase**: inventory, dependencies, migration strategy (lift-and-shift vs re-platform)
- **Planning phase**: migration waves, cutover strategy, rollback plans
- **Execution phase**: migrate in waves, validate, optimize
- **Optimization phase**: cost optimization, performance tuning, security hardening

### Data Platform Projects

- **Medallion architecture**: Bronze (raw) → Silver (cleaned) → Gold (curated)
- **Data quality**: quality gates, validation, quarantine processes
- **Governance**: catalog, lineage, access controls, compliance
- **Serving layer**: BI tools, APIs, data products

### Best Practices

- **Incremental value**: deliver value in phases, not big-bang
- **Data quality first**: establish quality standards early, enforce in pipelines
- **Cost awareness**: monitor cloud costs, optimize continuously
- **Security by design**: security from day one, not bolted on later

---

## 6. Security & Compliance Projects

### Security Project Types

- **Identity & access**: SSO, MFA, role-based access, privileged access management
- **Network security**: firewalls, segmentation, zero trust
- **Data protection**: encryption, data loss prevention, backup/recovery
- **Security monitoring**: SIEM, threat detection, incident response

### Compliance Projects

- **Regulatory compliance**: GDPR, HIPAA, SOC 2, PCI-DSS, SOX, CCPA
- **Audit readiness**: controls, documentation, evidence collection, audit trails
- **Policy implementation**: security policies, data governance, access policies
- **Enterprise frameworks**: NIST Cybersecurity Framework, ISO 27001, COBIT, ITIL

### PM Considerations

- **Security by design**: security requirements from start, not add-on
- **Compliance gates**: compliance checkpoints in project lifecycle
- **Risk management**: security risks are project risks; manage accordingly
- **Documentation**: security/compliance requires extensive documentation

---

## 7. DevOps Integration

### CI/CD Pipeline Projects

- **Pipeline setup**: build, test, deploy automation
- **Infrastructure as Code**: Terraform, Bicep, Ansible for infrastructure
- **Testing automation**: unit, integration, E2E tests in pipeline
- **Deployment automation**: blue-green, canary, feature flags

### PM in DevOps Context

- **Release cadence**: frequent, small releases vs infrequent, large releases
- **Deployment windows**: continuous deployment vs scheduled windows
- **Rollback strategy**: automated rollback, feature flags, canary rollbacks
- **Metrics**: deployment frequency, lead time, MTTR, change failure rate

### Best Practices

- **Automation investment**: invest in automation to reduce manual work, errors
- **Shift-left**: test early, security early, quality early
- **Blame-free culture**: failures are learning opportunities, not blame events
- **Continuous improvement**: measure, learn, improve (DevOps feedback loops)

---

## 8. Vendor & Third-Party Management

### Vendor Project Types

- **SaaS implementations**: CRM, ERP, collaboration tools
- **Custom development**: outsourced development, consulting engagements
- **Infrastructure vendors**: cloud providers, hardware vendors, managed services
- **Integration vendors**: API providers, data providers, middleware vendors

### Vendor Management Process

1. **Selection**: RFP, evaluation, contract negotiation
2. **Onboarding**: kickoff, access, tools, processes
3. **Execution**: regular checkpoints, deliverables, quality gates
4. **Offboarding**: knowledge transfer, access revocation, contract closure

### Enterprise Vendor Management

- **Multi-vendor coordination**: manage dependencies across vendors, coordinate integrations
- **Vendor risk assessment**: security assessments, financial stability, compliance verification
- **Master service agreements**: enterprise contracts, standardized terms, volume discounts
- **Vendor performance management**: SLAs, KPIs, regular reviews, corrective actions
- **Vendor governance**: vendor management office, vendor scorecards, relationship management

### Best Practices

- **Clear contracts**: SOW, SLAs, acceptance criteria, change process
- **Regular communication**: status meetings, escalation paths, relationship management
- **Quality gates**: review deliverables, test integrations, validate compliance
- **Risk management**: vendor risks are project risks; have backup plans
- **Enterprise procurement**: leverage enterprise contracts, follow procurement policies

---

## 9. IT-Specific Risks

### Technical Risks

- **Technology risks**: new/untested technology, scalability limits, integration complexity
- **Architecture risks**: design flaws, performance bottlenecks, security vulnerabilities
- **Dependency risks**: third-party APIs, open-source libraries, infrastructure dependencies
- **Data risks**: data quality, data loss, privacy breaches

### Operational Risks

- **Deployment risks**: production failures, rollback failures, data corruption
- **Performance risks**: slow performance, capacity limits, scalability issues
- **Security risks**: vulnerabilities, breaches, compliance failures
- **Vendor risks**: vendor failures, contract issues, support gaps

### Risk Mitigation Strategies

- **Proof of concepts**: validate technology/approach before full commitment
- **Architecture reviews**: peer reviews, external reviews, design patterns
- **Testing**: comprehensive testing (unit, integration, performance, security)
- **Monitoring**: observability, alerting, dashboards for early detection
- **Backup plans**: rollback procedures, alternative solutions, vendor backups

---

## 10. IT Project Success Patterns

### Success Factors

- **Clear requirements**: well-defined, prioritized, agreed-upon requirements
- **Technical feasibility**: validate approach early, proof of concepts
- **Stakeholder alignment**: business and IT aligned on goals, priorities, trade-offs
- **Team capability**: right skills, adequate capacity, motivated team
- **Quality focus**: quality from start, not bolted on later

### Common Failure Patterns

- **Scope creep**: uncontrolled expansion, unclear requirements
- **Technical debt**: shortcuts accumulate, block future work
- **Poor communication**: misaligned expectations, unclear status
- **Resource constraints**: understaffed, wrong skills, over-allocated
- **Integration issues**: underestimated complexity, dependencies

### PM Best Practices for IT

- **Technical partnership**: work closely with tech lead, respect technical decisions
- **Incremental delivery**: deliver value early, iterate based on feedback
- **Quality gates**: enforce quality standards, don't skip testing
- **Risk awareness**: identify technical risks early, plan mitigation
- **Continuous learning**: learn from failures, improve processes

---

## 11. Enterprise Architecture Alignment

### EA Integration

- **Architecture review**: align with enterprise architecture, review designs, ensure standards
- **Technology standards**: approved technologies, platforms, patterns, frameworks
- **Integration patterns**: API standards, messaging patterns, data integration, service mesh
- **Reference architectures**: reusable patterns, templates, best practices

### Enterprise Integration Projects

- **ESB/API Gateway**: enterprise service bus, API management, service orchestration
- **Data integration**: ETL/ELT patterns, data pipelines, real-time integration, batch processing
- **Legacy modernization**: legacy system integration, API wrappers, data migration
- **Microservices**: service decomposition, API design, service mesh, distributed systems

### Best Practices

- **Early architecture engagement**: involve EA team in planning, design reviews
- **Standards compliance**: follow enterprise standards, document exceptions
- **Reusability**: leverage existing services, patterns, components
- **Documentation**: architecture decisions, integration patterns, dependencies

---

## 12. IT Governance Frameworks

### COBIT (Control Objectives for Information and Related Technologies)

- **Governance framework**: IT governance, risk management, value delivery
- **Processes**: 40+ processes across governance and management domains
- **Maturity models**: assess IT capability, continuous improvement
- **Enterprise alignment**: align IT with business objectives, strategic planning

### ITIL (IT Infrastructure Library)

- **Service management**: service strategy, design, transition, operation, continual improvement
- **Service lifecycle**: service portfolio, service catalog, service level management
- **Change management**: change control, release management, configuration management
- **Incident management**: incident response, problem management, service restoration

### NIST Cybersecurity Framework

- **Security framework**: identify, protect, detect, respond, recover
- **Risk management**: cybersecurity risk assessment, controls, continuous monitoring
- **Compliance**: align with regulatory requirements, security standards
- **Maturity**: assess security maturity, improve capabilities

### Enterprise IT Governance

- **IT steering committee**: strategic IT decisions, portfolio management, resource allocation
- **Architecture review board**: architecture decisions, technology standards, design reviews
- **Change advisory board**: change approval, risk assessment, scheduling
- **Security governance**: security policies, compliance, risk management, incident response

### Best Practices

- **Framework adoption**: select appropriate framework, customize for organization
- **Maturity assessment**: assess current state, define target state, improvement roadmap
- **Integration**: integrate frameworks, avoid silos, align processes
- **Continuous improvement**: evolve governance, measure effectiveness, adapt

---

> **Note**: For general PM fundamentals, see [General Project Management](general.md). For methodology frameworks (Agile, Scrum, etc.), see [Project Management Methodologies](methodologies.md).

