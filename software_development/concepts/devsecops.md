# DevSecOps — Secure Software Delivery

**Scope**: Shift-left security practices, secure SDLC, supply chain security, security automation in CI/CD, and secure coding — bridging development, security, and operations.

**Purpose**: Use this when embedding security into the software delivery lifecycle. For general DevOps practices (CI/CD, IaC, deployment), see [DevOps](devops.md). For data-layer security and privacy controls, see [Data Security](../../data_management/concepts/core/security.md). For system-level security patterns (authn/authz, API gateways), see [Architecture](architecture.md).

## Table of Contents

- [1. DevSecOps Principles](#1-devsecops-principles)
- [2. Secure SDLC Integration](#2-secure-sdlc-integration)
- [3. Threat Modeling for Development](#3-threat-modeling-for-development)
- [4. Secure Coding Practices](#4-secure-coding-practices)
- [5. Supply Chain Security](#5-supply-chain-security)
- [6. Container & Runtime Security](#6-container--runtime-security)
- [7. Security Automation in CI/CD](#7-security-automation-in-cicd)
- [8. Infrastructure Security as Code](#8-infrastructure-security-as-code)
- [9. Secrets Management](#9-secrets-management)
- [10. Security Observability & Incident Response](#10-security-observability--incident-response)

---

## 1. DevSecOps Principles

### Core Principles

- **Shift Left**: Move security testing earlier in the SDLC — catch vulnerabilities at code time, not deploy time
- **Security as Code**: Express security policies, controls, and tests as versionable, reviewable code
- **Shared Responsibility**: Security is everyone's job — developers, operations, and security teams share ownership
- **Continuous Security**: Automate security validation at every stage; security is not a gate but a continuous signal
- **Least Privilege by Default**: Design systems, pipelines, and access around minimum necessary permissions

### DevSecOps vs Traditional Security

| Aspect | Traditional | DevSecOps |
|--------|-------------|-----------|
| **Timing** | Security review before release | Security integrated at every stage |
| **Ownership** | Dedicated security team | Shared across dev, sec, ops |
| **Testing** | Manual penetration testing | Automated scanning + manual review |
| **Feedback** | Weeks/months | Minutes (CI pipeline) |
| **Remediation** | Backlog tickets, deferred fixes | Fix in current sprint, block merge |
| **Compliance** | Periodic audits | Continuous compliance as code |

### Maturity Model

- **Level 1 — Reactive**: Ad-hoc security reviews, manual scans before release
- **Level 2 — Foundational**: SAST/SCA in CI, basic secret scanning, dependency updates
- **Level 3 — Integrated**: Threat modeling in design, security gates in pipelines, IaC scanning, SBOM generation
- **Level 4 — Optimized**: Policy-as-code enforcement, automated remediation, security observability, red-team exercises

---

## 2. Secure SDLC Integration

### Security Activities by Phase

| SDLC Phase | Security Activity | Output |
|------------|-------------------|--------|
| **Requirements** | Security requirements, abuse cases, compliance mapping | Security user stories, compliance checklist |
| **Design** | Threat modeling, architecture security review | Threat model document, mitigations |
| **Implementation** | Secure coding, peer review, pre-commit hooks | Reviewed code, linting results |
| **Build** | SAST, SCA, secret scanning, license checks | Scan reports, quality gates |
| **Test** | DAST, fuzzing, penetration testing | Vulnerability findings |
| **Deploy** | IaC scanning, image scanning, admission control | Deployment approval |
| **Operate** | Runtime monitoring, vulnerability management, patching | Alerts, incident reports |

### Security Requirements

- **Authentication**: Define auth mechanisms (MFA, SSO, certificate-based)
- **Authorization**: Define access control model (RBAC, ABAC, policy-based)
- **Data protection**: Classify data, define encryption requirements (at rest, in transit)
- **Audit**: Define logging and monitoring requirements
- **Compliance**: Map regulatory requirements (applicable privacy legislation, GDPR, SOC 2) to controls
- **Abuse cases**: Identify misuse scenarios alongside use cases

### Security Champions

- **Role**: Developer embedded in the team who advocates for security practices
- **Responsibilities**: Triage scan results, mentor peers on secure coding, liaise with security team
- **Training**: OWASP Top 10, secure coding for the team's language stack, threat modeling facilitation
- **Ratio**: One champion per team or per 5–8 developers

---

## 3. Threat Modeling for Development

### STRIDE Model

| Threat | Definition | Mitigation |
|--------|------------|------------|
| **Spoofing** | Impersonating a user or system | Authentication, certificates, MFA |
| **Tampering** | Modifying data or code | Integrity checks, signing, checksums |
| **Repudiation** | Denying an action occurred | Audit logging, immutable logs |
| **Information Disclosure** | Exposing data to unauthorized parties | Encryption, access controls, masking |
| **Denial of Service** | Making a service unavailable | Rate limiting, autoscaling, CDN |
| **Elevation of Privilege** | Gaining unauthorized access | Least privilege, input validation, RBAC |

### When to Threat Model

- **New services or components**: Before implementation begins
- **Significant changes**: New integrations, new data flows, auth changes
- **Incident-driven**: After a security incident reveals a gap
- **Periodic review**: Revisit models annually or when risk profile changes

### Lightweight Threat Modeling Process

1. **Scope**: Define system boundaries, data flows, trust boundaries
2. **Identify**: Enumerate threats using STRIDE or attack trees
3. **Prioritize**: Rate by likelihood × impact; focus on high-risk items
4. **Mitigate**: Define controls — preventive, detective, corrective
5. **Track**: Record threats as work items; verify mitigations in code review

### Attack Surface Analysis

- **Entry points**: APIs, web forms, file uploads, webhooks, message queues
- **Data flows**: Where sensitive data moves (user → API → DB → analytics)
- **Trust boundaries**: Network boundaries, service-to-service auth, third-party integrations
- **Privileged operations**: Admin endpoints, bulk exports, config changes

---

## 4. Secure Coding Practices

### OWASP Top 10 Mitigations

| Risk | Mitigation |
|------|------------|
| **Broken Access Control** | Server-side authorization checks, deny by default, RBAC enforcement |
| **Cryptographic Failures** | Use strong algorithms (AES-256, RSA-2048+), never roll your own crypto |
| **Injection** | Parameterized queries, ORMs, input validation, output encoding |
| **Insecure Design** | Threat modeling, secure design patterns, abuse case testing |
| **Security Misconfiguration** | Hardened defaults, remove unused features, automate config validation |
| **Vulnerable Components** | SCA scanning, dependency updates, SBOM tracking |
| **Auth Failures** | MFA, credential stuffing protection, secure session management |
| **Data Integrity Failures** | Code signing, integrity checks, secure deserialization |
| **Logging Failures** | Structured logging, log sensitive events, protect log integrity |
| **SSRF** | Allowlist outbound destinations, validate URLs, network segmentation |

### Input Validation

- **Validate on the server**: Never trust client-side validation alone
- **Allowlist over denylist**: Define what is valid, reject everything else
- **Type and range checks**: Validate data type, length, range, format
- **Encoding**: Encode output based on context (HTML, URL, SQL, OS command)
- **File uploads**: Validate file type, size, content; store outside web root

### Authentication & Session Management

- **Password storage**: Use bcrypt, scrypt, or Argon2 with per-user salts
- **Session tokens**: Cryptographically random, appropriate expiry, secure flags (HttpOnly, Secure, SameSite)
- **Token-based auth**: Short-lived access tokens, secure refresh token rotation
- **Account lockout**: Rate limiting + progressive delays over hard lockout (avoids DoS)

### Error Handling & Logging

- **Generic error messages**: Never expose stack traces, SQL errors, or internal paths to users
- **Structured security logs**: Log authentication events, authorization failures, input validation failures
- **Sensitive data in logs**: Never log passwords, tokens, PII, or encryption keys
- **Correlation IDs**: Trace requests across services for incident investigation

### Language-Specific Guidance

- **Python**: Use `parameterized` queries with DB-API, avoid `eval()`/`exec()`, use `secrets` module for tokens
- **C#**: Use `SqlParameter` for queries, `[Authorize]` attributes, `Data Protection API` for encryption
- **JavaScript/TypeScript**: Use `helmet` for HTTP headers, `DOMPurify` for XSS, avoid `innerHTML`
- **Go**: Use `html/template` (auto-escapes), `crypto/rand` for randomness, handle all errors explicitly
- **Java**: Use `PreparedStatement`, Spring Security for auth, avoid `ObjectInputStream` deserialization

---

## 5. Supply Chain Security

### Dependency Management

- **Pin versions**: Lock dependency versions (lock files: `package-lock.json`, `Pipfile.lock`, `go.sum`)
- **Automated updates**: Use Dependabot, Renovate, or similar for timely updates
- **Vulnerability scanning**: SCA tools (Snyk, Grype, `npm audit`, `pip-audit`) in CI
- **License compliance**: Check licenses for compatibility (SPDX identifiers)
- **Private registries**: Mirror approved packages internally for critical environments

### Software Bill of Materials (SBOM)

- **Generation**: Produce SBOM at build time (SPDX or CycloneDX format)
- **Contents**: All dependencies (direct + transitive), versions, licenses, provenance
- **Storage**: Attach SBOM as build artifact; store alongside release artifacts
- **Consumption**: Feed SBOMs into vulnerability management for continuous monitoring
- **Standards**: NTIA minimum elements, CISA SBOM guidance

### Artifact Integrity

- **Code signing**: Sign commits (GPG/SSH) and release artifacts
- **Image signing**: Sign container images (Cosign, Notation)
- **Provenance**: Generate build provenance attestations (SLSA framework)
- **Verification**: Verify signatures and provenance before deployment
- **Reproducible builds**: Deterministic builds to verify artifact integrity

### SLSA Framework (Supply-chain Levels for Software Artifacts)

- **Level 1**: Build process documented and generates provenance
- **Level 2**: Hosted build service, authenticated provenance
- **Level 3**: Hardened build platform, non-falsifiable provenance
- **Level 4**: Two-person review, hermetic builds, reproducible

---

## 6. Container & Runtime Security

### Image Security

- **Minimal base images**: Use distroless, Alpine, or scratch images to reduce attack surface
- **Image scanning**: Scan images for CVEs in CI (Trivy, Grype, Snyk Container)
- **No secrets in images**: Never embed credentials, keys, or config files in images
- **Image pinning**: Reference images by digest (`sha256:...`), not mutable tags
- **Trusted registries**: Pull from verified registries; enforce registry allowlists

### Container Hardening

- **Run as non-root**: Set `USER` in Dockerfile; enforce via security context
- **Read-only filesystem**: Mount root filesystem as read-only where possible
- **Drop capabilities**: Drop all Linux capabilities, add back only what's needed
- **Resource limits**: Set CPU/memory limits to prevent resource exhaustion
- **No privilege escalation**: Set `allowPrivilegeEscalation: false`

### Kubernetes Security

- **Network policies**: Default-deny ingress/egress; allowlist required traffic
- **Pod security standards**: Enforce `restricted` profile (no root, no host namespaces)
- **RBAC**: Namespace-scoped roles, avoid `cluster-admin` for workloads
- **Admission controllers**: Use OPA/Gatekeeper or Kyverno to enforce policies
- **Secrets**: Use external secret stores (Vault, Key Vault) rather than K8s secrets in etcd
- **Service mesh**: mTLS between services (Istio, Linkerd) for zero-trust networking

### Runtime Protection

- **Runtime scanning**: Monitor for anomalous process execution, file access, network connections
- **Immutable infrastructure**: Replace containers rather than patching in-place
- **Drift detection**: Alert when running containers differ from approved images
- **Syscall filtering**: Use seccomp profiles to restrict available system calls

---

## 7. Security Automation in CI/CD

### Pipeline Security Stages

```
Code → Pre-commit → Build → Test → Stage → Deploy → Monitor
  │        │          │       │       │        │        │
  │    Secret scan  SAST    DAST   Image    Admit    Runtime
  │    Lint         SCA     Fuzz   scan     control  scan
  │                 SBOM    Pen
  │                 License test
  └── IDE plugins
```

### Static Application Security Testing (SAST)

- **When**: Build stage, PR validation
- **Tools**: SonarQube, CodeQL, Semgrep, Checkmarx
- **Scope**: Source code analysis for vulnerabilities (injection, XSS, hardcoded secrets)
- **Tuning**: Suppress false positives with inline annotations; track suppression rate

### Software Composition Analysis (SCA)

- **When**: Build stage, scheduled scans
- **Tools**: Snyk, Grype, OWASP Dependency-Check, GitHub Dependabot
- **Scope**: Known vulnerabilities in dependencies (direct and transitive)
- **Policy**: Define severity thresholds — block on critical/high, warn on medium

### Dynamic Application Security Testing (DAST)

- **When**: Test/staging stage (requires running application)
- **Tools**: OWASP ZAP, Burp Suite, Nuclei
- **Scope**: Runtime vulnerability detection (XSS, SQLI, misconfigurations)
- **Integration**: Run against deployed staging environment in pipeline

### Secret Scanning

- **Pre-commit**: GitLeaks, detect-secrets as pre-commit hooks
- **CI scanning**: Scan full repo history for leaked secrets
- **Prevention**: Use `.gitignore` patterns, secret detection in IDE
- **Response**: Rotate any detected secret immediately; treat as compromised

### Infrastructure as Code Scanning

- **Tools**: Checkov, tfsec, KICS, Bridgecrew
- **Scope**: Misconfigurations in Terraform, Bicep, ARM, Kubernetes manifests, Dockerfiles
- **Policy**: Enforce security baselines (encryption enabled, no public endpoints, logging configured)
- **Integration**: Run in PR validation; block merge on critical findings

### Quality Gates

- **Gate criteria**: Zero critical/high vulnerabilities, SBOM generated, all scans passed
- **Break-the-build**: Fail pipeline on policy violations (configurable severity threshold)
- **Exceptions**: Documented risk-acceptance for false positives or accepted risks
- **Metrics**: Track gate pass rate, mean time to remediate, vulnerability backlog age

---

## 8. Infrastructure Security as Code

### Policy as Code

- **Definition**: Express security policies as code — version-controlled, testable, enforceable
- **Tools**: OPA/Rego, Azure Policy, AWS Config Rules, Sentinel (Terraform)
- **Patterns**: Deny non-compliant resources at deploy time; alert on drift at runtime

### Guardrails vs Gates

| Approach | Mechanism | Effect |
|----------|-----------|--------|
| **Guardrails** | Preventive policies baked into templates/modules | Non-compliant config impossible to express |
| **Gates** | Policy checks at deployment time | Block deployment if checks fail |
| **Monitoring** | Continuous compliance scanning | Detect and alert on drift post-deployment |

### Compliance as Code

- **Regulatory mapping**: Map controls to regulatory requirements (NIST, ISO 27001, SOC 2)
- **Automated evidence**: Generate compliance evidence from pipeline runs and scan results
- **Continuous audit**: Replace periodic manual audits with continuous automated checks
- **Drift remediation**: Auto-remediate or alert when infrastructure drifts from policy

### Secure Defaults in IaC

- **Encryption**: Enable encryption at rest and in transit by default in all modules
- **Networking**: Private endpoints by default; no public IPs unless explicitly required
- **Logging**: Enable diagnostic logging and audit trails in every resource module
- **Access**: Managed identities over keys; RBAC over shared access signatures
- **Tagging**: Enforce classification and ownership tags on all resources

---

## 9. Secrets Management

### Secret Lifecycle

- **Generation**: Use cryptographically secure random generation; never manual/predictable secrets
- **Storage**: Centralized vault (Azure Key Vault, HashiCorp Vault, AWS Secrets Manager)
- **Access**: Application retrieves secrets at runtime; never baked into images, config, or code
- **Rotation**: Automated rotation on schedule; support graceful key rollover (old + new valid during transition)
- **Revocation**: Immediate revocation capability; automated on compromise detection

### Secret Hygiene

- **No secrets in source control**: Pre-commit hooks to prevent accidental commits
- **No secrets in environment variables for production**: Use secret store references
- **No secrets in logs**: Redact or mask secrets in all log outputs
- **No secrets in error messages**: Ensure exceptions don't expose secret values
- **No shared secrets**: Per-service, per-environment credentials

### Zero-Trust Secrets Access

- **Identity-based access**: Services authenticate to vault via managed identity, not shared keys
- **Short-lived credentials**: Prefer dynamic secrets with TTL over static long-lived credentials
- **Audit trail**: Log every secret access (who, when, which secret, from where)
- **Network restrictions**: Restrict vault access to known networks/service endpoints

### Pipeline Secrets

- **Secure variables**: Use pipeline secret variables (masked in logs, encrypted at rest)
- **Service connections**: Use managed identity or federated credentials for cloud access
- **Ephemeral credentials**: Generate short-lived tokens for pipeline runs
- **No secrets in artifacts**: Validate that build artifacts contain no embedded secrets

---

## 10. Security Observability & Incident Response

### Security Monitoring

- **Application logs**: Authentication events, authorization failures, input validation rejections
- **Infrastructure logs**: Network flows, resource access, configuration changes
- **Pipeline logs**: Scan results, gate decisions, deployment approvals
- **Correlation**: Unified log aggregation with correlation IDs across services

### Detection Patterns

- **Anomaly detection**: Unusual API call volumes, geographic anomalies, privilege escalation
- **Known indicators**: Signature-based detection for known attack patterns (WAF rules, IDS)
- **Behavioral analysis**: Baseline normal behavior; alert on deviations
- **Threat intelligence**: Feed external threat intel into detection rules

### Security Metrics

| Metric | Target | Why It Matters |
|--------|--------|----------------|
| **Mean time to detect (MTTD)** | < 24 hours | Fast detection limits blast radius |
| **Mean time to remediate (MTTR)** | < 72 hours (critical) | Rapid fix reduces exposure window |
| **Vulnerability backlog age** | < 30 days (high) | Aging vulnerabilities increase risk |
| **Pipeline gate pass rate** | > 90% | Low rates indicate systemic issues |
| **Dependency currency** | < 1 major version behind | Stale dependencies accumulate CVEs |
| **Secret rotation compliance** | 100% on schedule | Expired/stale secrets are attack vectors |

### Incident Response for Development Teams

- **Runbooks**: Pre-written response procedures for common scenarios (credential leak, vulnerable dependency, compromised build)
- **War rooms**: Dedicated communication channel for active incidents
- **Rollback capability**: Ability to revert to last-known-good deployment within minutes
- **Post-incident review**: Blameless retrospective; update threat models and controls
- **Simulation**: Regular game-day exercises (tabletop, red team, chaos engineering)

### Vulnerability Management Workflow

1. **Ingest**: Scan results flow into a central vulnerability tracker
2. **Triage**: Deduplicate, assess exploitability, assign severity and owner
3. **Prioritize**: Risk-based prioritization (CVSS + exploitability + asset criticality)
4. **Remediate**: Fix in code, update dependency, apply compensating control
5. **Verify**: Re-scan to confirm fix; close finding
6. **Report**: Dashboard visibility for leadership; trend analysis over time

---

> **Note**: For CI/CD pipeline patterns and deployment strategies, see [DevOps](devops.md). For Azure DevOps platform-specific security features, see [Azure DevOps](azure_devops.md). For security tools and standards references, see [Standards](../../_resources/standards.md) and [Tools](../../_resources/tools.md). For data-layer security and privacy controls, see [Data Security](../../data_management/concepts/core/security.md).
