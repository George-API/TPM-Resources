# Modern Data Management Best Practices — Enterprise Overview

**Scope**: Domain-agnostic data management concepts and best practices (platform-independent).

## Table of Contents

- [1. Data Management Operating Model](#1-data-management-operating-model)
- [2. Data Security (Advanced Controls)](#2-data-security-advanced-controls)
- [3. Data Privacy (Advanced)](#3-data-privacy-advanced)
- [4. Data Quality (Advanced System)](#4-data-quality-advanced-system)
- [5. Data Integrity (Advanced Patterns)](#5-data-integrity-advanced-patterns)
- [6. Metadata, Catalog, and Lineage (Advanced)](#6-metadata-catalog-and-lineage-advanced)
- [7. Data Lifecycle & Retention (Advanced)](#7-data-lifecycle--retention-advanced)
- [8. Reliability, Resilience, and Data SRE (Advanced)](#8-reliability-resilience-and-data-sre-advanced)
- [9. Data Governance Control Plane (Advanced)](#9-data-governance-control-plane-advanced)
- [10. Advanced "What Good Looks Like" Checklist](#10-advanced-what-good-looks-like-checklist)

---

## 1. Data Management Operating Model (how it actually runs)

### Federated governance (modern default)

- Central team sets standards, controls, tooling (catalog, policy, identity).
- Domain teams own data products (meaning, SLAs, quality, access decisions).
- Platform team owns reliability, shared services, guardrails, landing zones.

### Data product minimum bar (publishable dataset)

- Owner + steward + support channel
- Contract: schema + semantics + keys + SLAs + versioning policy
- Quality checks + quality dashboard
- Lineage recorded + documentation + examples
- Access model: roles, RLS/CLS/masking rules, approved use cases

### Certification levels

- **Draft**: exploratory; no SLA
- **Verified**: checks exist; limited SLA
- **Certified**: governed, stable contract, SLOs, monitored, approved for BI

---

## 2. Data Security (Advanced Controls)

### Threat-model mindset

- Who can exfiltrate? (insiders, compromised identities, misconfigurations)
- What is the blast radius? (tenant/domain isolation, segmentation)

### Ontario-specific requirements

- **GO-ITS security standards**: Adhere to Government of Ontario IT security standards for public sector systems
- **FIPPA compliance**: Security controls must protect personal information per Freedom of Information and Protection of Privacy Act

### Advanced access patterns

- **Attribute-based access control (ABAC)**: policy based on attributes (domain, sensitivity, geography)
- **Break-glass accounts**: tightly controlled, monitored, time-bound, reviewed
- **Just-in-time access** for data stores (admin + elevated read paths)

### Sensitive data handling

- Per-domain "safe views": curated, masked extracts as default for analysts
- **Double-key encryption patterns** (advanced): org key + domain key for higher assurance
- **Data loss prevention (DLP)** policies for exports/downloads where supported

### Auditing

- Immutable audit log sinks (append-only) for regulated access trails
- **Periodic access recertification**: dataset access must be re-approved on schedule
- **Alert patterns**: unusual volume, unusual time, unusual geography, privilege escalation

---

## 3. Data Privacy (Advanced)

### Privacy by design patterns

- **Separation of duties**: only limited roles can access raw identifiers (PII)
- **De-identification ladder**:
  - masking → pseudonymization → tokenization → aggregation → synthetic data (as required)
- **Consent + purpose tracking**:
  - attach "allowed usage" metadata to datasets (policy enforcement where possible)
- **Right-to-delete workflows** (if applicable):
  - locate identifiers across lake/warehouse + propagate deletion/soft-delete markers + audit proof

### Ontario-specific requirements

- **FIPPA**: Freedom of Information and Protection of Privacy Act governs collection, use, and disclosure of personal information by public institutions
- **PHIPA**: Personal Health Information Protection Act applies to personal health information (health information custodians)
- **Purpose limitation**: Collect and use personal information only for stated purposes (FIPPA/PHIPA requirement)
- **Access rights**: Individuals have right to access their personal information and request corrections (FIPPA/PHIPA)

---

## 4. Data Quality (Advanced System)

### Quality gates (where checks become enforceable)

- **Ingestion gate (Bronze)**: schema/format validation + source availability checks
- **Transformation gate (Silver)**: standardization + dedupe + domain rules
- **Serving gate (Gold)**: KPI sanity + reconciliations + referential checks

### Rule categories (build a balanced set)

- **Structural**: schema, types, required columns
- **Content**: ranges, enums, regex formats, cross-field rules
- **Relationship**: uniqueness, referential integrity, join coverage
- **Statistical**: drift detection (distribution shifts, sudden spikes/drops)
- **Timeliness**: freshness SLA compliance, late-arrival rates

### Quarantine + defect lifecycle (critical for maturity)

- Bad records routed to quarantine with reason codes
- "Defect backlog" owned by domain steward; SLA for remediation
- Replay/reprocess tooling for corrected data

### Sampling & reconciliation (to avoid blind spots)

- **Source-to-target reconciliation**: counts, sums, checksums, coverage
- **Partition-level reconciliation**: daily totals vs prior day/rolling baselines
- "Control totals" from source systems when available

---

## 5. Data Integrity (Advanced Patterns)

### Keys and identity

- Canonical business keys defined in contract (natural keys where stable)
- **Surrogate keys**:
  - deterministic hash keys to stabilize across reruns/backfills
- **Deduplication**:
  - explicit survivorship rules (latest timestamp, highest priority source, non-null preference)

### Temporal integrity

- **Late arriving data**: watermark strategy + allowed lateness window
- **Slowly changing dimensions (SCD)**:
  - Type 1 (overwrite), Type 2 (history), Type 3 (limited history)
- **Effective-dated joins**: ensure facts map to correct dimension version

### Exactly-once reality

- Assume at-least-once delivery; enforce idempotency
- **Outbox/inbox patterns**:
  - outbox ensures "DB write + event publish" consistency
  - inbox prevents double-processing on consumer side

### Immutability & auditability

- Bronze append-only; never edit raw (store corrections as new records)
- **Reproducible transforms**:
  - version code + config + dependency versions for every build/run

---

## 6. Metadata, Catalog, and Lineage (Advanced)

### Metadata that matters

- **Technical**: schema, partitions, formats, owners, SLAs, retention, sensitivity tags
- **Business**: glossary terms, KPI definitions, calculation logic, "source of truth" flags
- **Operational**: pipeline owners, run frequency, failure modes, incident history

### Lineage maturity

- **Pipeline lineage**: dataset A → transformation → dataset B
- **Column-level lineage** (advanced): required for regulated reporting / audits
- **Data observability lineage**: freshness and quality metrics tied to assets

### Documentation standards

- "Getting started" examples per dataset:
  - sample queries, common joins, pitfalls, edge cases
- **Changelog**:
  - schema changes, deprecations, breaking changes with dates

### Data discovery patterns

- **Search**: full-text search across metadata, schemas, documentation, tags
- **Browsing**: hierarchical navigation by domain, data product, source system
- **Recommendations**: "similar datasets", "frequently used together", "trending"
- **Tags & classifications**: domain tags, sensitivity labels, certification status for filtering

---

## 7. Data Lifecycle & Retention (Advanced)

### Retention by zone (example model)

- **Bronze**: shorter retention if sourced elsewhere + storage cost constraints
- **Silver**: moderate retention (replay/backfill window)
- **Gold**: retention aligned to reporting/regulatory requirements

### Deletion patterns

- Hard delete (remove) vs soft delete (tombstone) vs crypto-shredding (key destruction)
- Legal hold overrides and audit trails for exceptions
- Archive tiers for cold partitions + restore procedures tested

### Ontario-specific requirements

- **FIPPA retention**: Retain personal information only as long as necessary for the purpose (FIPPA requirement)
- **Disposal requirements**: Securely dispose of personal information when no longer needed (FIPPA/PHIPA)
- **Record retention schedules**: Follow approved retention schedules for government records

---

## 8. Reliability, Resilience, and Data SRE (Advanced)

### SLOs & error budgets (data-specific)

- **Freshness SLO**: % of partitions updated within SLA window
- **Completeness SLO**: % of expected records received
- **Quality SLO**: % of critical checks passing
- **Availability SLO**: query/serving uptime for consumers

### Backpressure & scaling

- **Stream lag alerts**: consumer lag thresholds + autoscale rules
- **Queue depth alerting**: scale consumers; throttle producers if necessary
- **Concurrency limits**: prevent DB connection storms and SNAT exhaustion

### Incident playbooks (minimum set)

- **Freshness breach**: identify dependency, rerun safe path, communicate ETA, prevent recurrence
- **Schema break**: block pipeline, quarantine, negotiate contract change, version bump
- **DLQ spike**: classify transient/permanent, replay, add idempotency fix if needed

---

## 9. Data Governance Control Plane (Advanced)

### Policy-driven controls

- Access policies based on sensitivity + domain + purpose
- "Data product registration" required before broad access is granted
- **Automated enforcement**:
  - deny public endpoints where applicable
  - require owners/tags/retention policies
  - require quality checks for certification

### Ontario-specific requirements

- **GO-ITS standards**: Adhere to Government of Ontario IT Standards for data management, security, and interoperability
- **Open Data Directive**: Government data open by default unless exempted (privacy, security, confidentiality, commercial sensitivity)
- **Anti-Racism Act**: Race-based data collection requirements for public sector organizations (identify and monitor racial disparities)

### Change management

- **Contract testing in CI**:
  - schema compatibility checks
  - sample payload validation
  - quality unit tests for transforms
- **Data testing patterns**:
  - unit tests for transform logic (business rules, calculations)
  - integration tests for pipeline runs (end-to-end validation)
  - data validation tests (referential integrity, constraints, expected ranges)
- **Deprecation lifecycle**:
  - announce → dual-run → cutover → retire (with dates)

---

## 10. Advanced "What Good Looks Like" Checklist

### Security/Privacy

- Sensitive datasets default to masked safe views
- Strong auditing + anomaly detection + access recertification
- Purpose/consent metadata tracked where required

### Quality

- Quality gates + quarantine + defect lifecycle
- Drift detection on key measures
- Reconciliation to source control totals

### Integrity

- Deterministic keys + survivorship rules
- Temporal correctness (late arrivals, SCD, effective dating)
- Idempotent pipelines + outbox/inbox where needed

### Governance

- Data products with contracts + certification tiers
- Catalog + lineage + change logs kept current
- Standards enforced via policy + CI gates

### Ops

- Data SLOs + error budgets + runbooks
- Replay/backfill procedures tested
- Cost observability (cost per dataset/pipeline/tenant)

---

> **Implementation**: See [Microsoft Data Platform Implementation](ms_architecture.md) for Azure/Fabric-specific guidance on implementing these practices.
