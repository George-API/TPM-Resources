# Cybersecurity & Security Operations

**Scope**: Enterprise cybersecurity operations, security governance, identity and network security, cloud security posture, offensive security, incident response, and forensics.

**Purpose**: Use this when working with security teams, evaluating organizational security posture, or understanding the operational security landscape around software systems. For embedding security into the development pipeline (SAST, SCA, DAST, secure coding, supply chain), see [DevSecOps](devsecops.md). For data-layer security and privacy, see [Data Security](../../data_management/concepts/core/security.md). For system-level auth patterns, see [Architecture](architecture.md).

## Table of Contents

- [1. Security Frameworks & Governance](#1-security-frameworks--governance)
- [2. Identity & Access Security](#2-identity--access-security)
- [3. Network Security](#3-network-security)
- [4. Cloud Security](#4-cloud-security)
- [5. Endpoint Security](#5-endpoint-security)
- [6. Security Operations Center (SOC)](#6-security-operations-center-soc)
- [7. SIEM & SOAR](#7-siem--soar)
- [8. Offensive Security](#8-offensive-security)
- [9. Incident Response & Forensics](#9-incident-response--forensics)
- [10. Security Awareness & Culture](#10-security-awareness--culture)

---

## 1. Security Frameworks & Governance

### NIST Cybersecurity Framework (CSF)

Five core functions that organize security activities across an enterprise.

```
  Identify ──▶ Protect ──▶ Detect ──▶ Respond ──▶ Recover
  ─────────    ─────────    ────────   ─────────   ─────────
  Asset mgmt   Access ctrl  Monitoring Containment Restore
  Risk assess  Awareness    Detection  Analysis    Lessons
  Governance   Data sec     Anomalies  Mitigation  Comms
```

| Function | Focus | Key Activities |
|----------|-------|----------------|
| **Identify** | What do we have and what are the risks? | Asset inventory, risk assessment, supply chain mapping, governance |
| **Protect** | What safeguards are in place? | Access control, training, data security, maintenance, protective tech |
| **Detect** | How do we find incidents? | Continuous monitoring, anomaly detection, event analysis |
| **Respond** | What do we do when something happens? | Response planning, communications, analysis, mitigation |
| **Recover** | How do we restore capability? | Recovery planning, improvements, communications |

### ISO 27001 Information Security Management

| Component | Purpose |
|-----------|---------|
| **ISMS scope** | Define boundaries of the security management system |
| **Risk assessment** | Identify threats, vulnerabilities, and impacts systematically |
| **Statement of Applicability** | Map selected controls to identified risks |
| **Annex A controls** | 93 controls across 4 themes: organizational, people, physical, technological |
| **Internal audit** | Verify ISMS effectiveness; identify non-conformities |
| **Management review** | Leadership evaluates ISMS performance and directs improvements |
| **Continual improvement** | Correct non-conformities, improve controls based on evidence |

### CIS Controls v8

18 prioritized controls grouped by implementation difficulty.

| Group | Controls | Examples |
|-------|----------|----------|
| **Implementation Group 1** | Basic hygiene (all orgs) | Asset inventory, access control, secure config, audit logging, malware defense |
| **Implementation Group 2** | Growing complexity | Data protection, email/browser security, vulnerability management, network monitoring |
| **Implementation Group 3** | Mature capability | Penetration testing, security awareness, application security, incident management |

### Risk Assessment

| Step | Activity | Output |
|------|----------|--------|
| **1. Scope** | Define system boundaries, data flows, stakeholders | Assessment scope document |
| **2. Identify** | Enumerate threats, vulnerabilities, assets | Threat/vulnerability inventory |
| **3. Analyze** | Estimate likelihood and impact (qualitative or quantitative) | Risk ratings |
| **4. Evaluate** | Compare against risk tolerance; prioritize | Ranked risk register |
| **5. Treat** | Accept, mitigate, transfer, or avoid each risk | Treatment plan with owners and deadlines |
| **6. Monitor** | Track residual risk, reassess periodically | Updated risk register |

### Security Governance

- **Security policies**: High-level intent and direction (acceptable use, data classification, incident response)
- **Standards**: Mandatory requirements that implement policies (password length, encryption algorithms, patching cadence)
- **Procedures**: Step-by-step instructions for specific tasks (account provisioning, incident escalation)
- **Guidelines**: Recommended practices that support standards (secure coding tips, configuration hardening)
- **Metrics for leadership**: Report on risk posture, compliance status, incident trends, remediation velocity

---

## 2. Identity & Access Security

### IAM Lifecycle

```
  Provision ──▶ Authenticate ──▶ Authorize ──▶ Monitor ──▶ Deprovision
      │              │               │             │             │
  Onboarding     MFA, SSO,       RBAC, ABAC,    Access       Offboarding,
  role assign    passwordless    least privilege  reviews      revocation
```

| Phase | Key Controls |
|-------|-------------|
| **Provision** | Automated onboarding, role-based assignment, approval workflows |
| **Authenticate** | MFA, SSO (SAML, OIDC), passwordless (FIDO2, passkeys), conditional access |
| **Authorize** | RBAC, ABAC, just-in-time access, scope-limited tokens |
| **Monitor** | Access reviews, anomaly detection, impossible travel alerts |
| **Deprovision** | Automated offboarding, immediate revocation on termination, periodic cleanup |

### Privileged Access Management (PAM)

- **Just-in-time (JIT) access**: Elevate privileges only when needed, with time limits and approval
- **Session recording**: Record privileged sessions for audit and forensic review
- **Credential vaulting**: Store privileged credentials in a vault; never expose directly
- **Break-glass accounts**: Emergency access with mandatory post-use review
- **Standing privilege elimination**: Replace permanent admin accounts with JIT workflows

### Identity Threat Detection and Response (ITDR)

| Signal | Indicates |
|--------|-----------|
| Impossible travel | Compromised credentials |
| Bulk permission changes | Privilege escalation attempt |
| Service account interactive login | Credential misuse |
| Dormant account activation | Potential lateral movement |
| Auth failures followed by success | Credential stuffing or brute force |

### Zero Trust Identity Principles

- **Verify explicitly**: Authenticate and authorize based on all available signals (identity, location, device, risk)
- **Least privilege access**: Scope permissions to the minimum required; use time-bound elevation
- **Assume breach**: Design as if the perimeter is already compromised; segment and monitor laterally

---

## 3. Network Security

### Defense Layers

```
  Internet ──▶ DDoS Protection ──▶ WAF ──▶ Firewall ──▶ Load Balancer
                                                              │
                                                     ┌────────┴────────┐
                                                     │   DMZ / Edge    │
                                                     └────────┬────────┘
                                                              │
                                              ┌───────────────┼───────────────┐
                                              │               │               │
                                          App Tier        Data Tier      Mgmt Tier
                                         (segmented)     (segmented)    (segmented)
```

### Firewalls & Segmentation

| Control | Purpose |
|---------|---------|
| **Network firewall** | Filter traffic between zones based on rules (L3/L4) |
| **Web Application Firewall (WAF)** | Inspect HTTP traffic for application-layer attacks (L7) |
| **Network segmentation** | Isolate workloads into zones to limit lateral movement |
| **Micro-segmentation** | Enforce per-workload policies (e.g., service mesh mTLS, NSGs) |
| **Network Security Groups (NSGs)** | Cloud-native L3/L4 filtering at subnet or NIC level |
| **Private endpoints** | Expose cloud services on private IP; eliminate public internet exposure |

### Intrusion Detection and Prevention

| Type | Mechanism | Deployment |
|------|-----------|------------|
| **IDS** | Monitors and alerts on suspicious traffic | Passive, out-of-band |
| **IPS** | Monitors and blocks suspicious traffic inline | Inline, active |
| **Host-based (HIDS/HIPS)** | Monitors individual host activity | Agent on endpoint |
| **Network-based (NIDS/NIPS)** | Monitors network segments | Network tap or span port |

### Zero Trust Network Architecture

- **No implicit trust**: Every connection is authenticated and authorized regardless of network location
- **Identity-based access**: Access decisions based on user/device identity, not IP address
- **Micro-perimeters**: Each application or service is its own trust boundary
- **Continuous validation**: Re-evaluate trust on every request, not just at session start
- **Encrypted everywhere**: TLS/mTLS for all traffic, including east-west within the network

---

## 4. Cloud Security

### Cloud Security Posture Management (CSPM)

| Capability | What It Does |
|------------|-------------|
| **Configuration assessment** | Continuously scan cloud resources against security benchmarks (CIS, NIST) |
| **Compliance mapping** | Map findings to regulatory frameworks and report compliance status |
| **Misconfiguration detection** | Identify public storage, open ports, missing encryption, overly permissive IAM |
| **Drift detection** | Alert when resource configuration deviates from approved baseline |
| **Remediation** | Auto-remediate or generate fix recommendations with priority |

### Cloud-Native Application Protection Platform (CNAPP)

CNAPP converges multiple cloud security capabilities into one platform.

| Component | Scope |
|-----------|-------|
| **CSPM** | Cloud configuration and compliance |
| **CWPP** | Workload protection (VMs, containers, serverless) |
| **CIEM** | Cloud infrastructure entitlement management (excess permissions) |
| **IaC scanning** | Security checks on Terraform, Bicep, ARM before deployment |
| **Runtime protection** | Detect threats in running workloads |

### Shared Responsibility Model

| Layer | IaaS | PaaS | SaaS |
|-------|------|------|------|
| **Physical** | Provider | Provider | Provider |
| **Network** | Shared | Provider | Provider |
| **OS** | Customer | Provider | Provider |
| **Application** | Customer | Customer | Provider |
| **Data** | Customer | Customer | Customer |
| **Identity** | Customer | Customer | Customer |

### Cloud Security Principles

- **Least privilege IAM**: Scope policies to specific resources and actions; avoid wildcards
- **Encryption by default**: Enable encryption at rest and in transit for all services
- **Logging everything**: Enable cloud audit trails (CloudTrail, Azure Activity Log, GCP Audit Logs)
- **Network isolation**: Private endpoints, VNet/VPC, service endpoints over public access
- **Immutable infrastructure**: Replace rather than patch; drift = compromise signal

---

## 5. Endpoint Security

### Endpoint Detection and Response (EDR)

| Capability | Purpose |
|------------|---------|
| **Real-time monitoring** | Observe process execution, file access, network connections, registry changes |
| **Behavioral analysis** | Detect anomalous patterns (fileless malware, living-off-the-land techniques) |
| **Threat hunting** | Proactive search for indicators of compromise across endpoints |
| **Automated response** | Isolate endpoint, kill process, quarantine file on detection |
| **Forensic data** | Retain telemetry for investigation (process trees, file hashes, network connections) |

### Extended Detection and Response (XDR)

XDR extends EDR by correlating telemetry across endpoints, network, cloud, identity, and email into a unified detection and investigation platform.

| EDR | XDR |
|-----|-----|
| Endpoint telemetry only | Cross-domain correlation |
| Endpoint-scoped response | Coordinated response across layers |
| Manual correlation for full picture | Automated correlation and enrichment |

### Host Hardening

- **Patch management**: Automated, timely patching with rollback capability
- **Configuration baselines**: CIS benchmarks, Microsoft Security Baselines, DISA STIGs
- **Application allowlisting**: Only approved executables can run
- **Disable unnecessary services**: Reduce attack surface by removing unneeded features
- **Local admin removal**: Eliminate standing local admin; use PAM for elevation

---

## 6. Security Operations Center (SOC)

### SOC Structure

```
  Tier 1 (Analyst)           Tier 2 (Investigator)        Tier 3 (Threat Hunter)
  ──────────────────         ─────────────────────         ──────────────────────
  Alert triage               Deep investigation            Proactive threat hunting
  Initial classification     Correlation across sources    Adversary emulation
  Playbook execution         Root cause analysis           Detection engineering
  Escalation to Tier 2       Containment actions           Tool and rule development
```

### SOC Workflow

1. **Ingest**: Collect logs and telemetry from endpoints, network, cloud, identity, applications
2. **Detect**: SIEM correlation rules and ML models generate alerts
3. **Triage**: Tier 1 classifies alert severity, eliminates false positives
4. **Investigate**: Tier 2 correlates evidence, determines scope and impact
5. **Contain**: Isolate affected systems, revoke compromised credentials
6. **Remediate**: Remove threat, patch vulnerability, restore from known-good state
7. **Report**: Document findings, update detection rules, brief stakeholders

### SOC Metrics

| Metric | Purpose | Target |
|--------|---------|--------|
| **Mean time to detect (MTTD)** | Speed of threat identification | < 24 hours |
| **Mean time to respond (MTTR)** | Speed of containment and remediation | < 4 hours (critical) |
| **Alert volume** | Workload indicator; rising volume may signal tuning issues | Trending stable or down |
| **False positive rate** | Detection quality; high rates cause alert fatigue | < 30% |
| **Escalation rate** | Proportion of Tier 1 alerts escalated to Tier 2 | 10-20% |
| **Dwell time** | How long a threat persists before detection | Decreasing quarter over quarter |

---

## 7. SIEM & SOAR

### SIEM (Security Information and Event Management)

| Function | Description |
|----------|-------------|
| **Log aggregation** | Centralize logs from all sources (endpoints, network, cloud, apps, identity) |
| **Normalization** | Parse and standardize disparate log formats into a common schema |
| **Correlation** | Match events across sources using rules and behavioral analytics |
| **Alerting** | Generate prioritized alerts based on correlation rules and threat intelligence |
| **Dashboards** | Visualize security posture, trends, and active incidents |
| **Retention** | Store logs for compliance, forensics, and trend analysis |

### SIEM Effectiveness

- **Tune rules iteratively**: Start broad, refine based on false positive analysis
- **Enrich events**: Augment alerts with asset context, threat intel, vulnerability data
- **Use case-driven detection**: Build rules around specific attack scenarios (credential stuffing, lateral movement, data exfiltration)
- **Log source coverage**: Map log sources to MITRE ATT&CK techniques to identify detection gaps
- **Cost management**: High-volume, low-value logs (debug, health checks) increase cost without improving detection

### SOAR (Security Orchestration, Automation, and Response)

| Capability | Purpose |
|------------|---------|
| **Playbook automation** | Codify and automate repeatable response actions (block IP, disable account, quarantine host) |
| **Orchestration** | Coordinate actions across multiple tools (SIEM, EDR, firewall, ticketing) |
| **Case management** | Track incidents from detection through resolution with evidence and timeline |
| **Enrichment** | Auto-enrich alerts with context (threat intel lookups, asset owner, vulnerability status) |
| **Metrics** | Measure automation rates, response times, analyst workload |

### MITRE ATT&CK Integration

- **Detection mapping**: Map SIEM rules and EDR detections to ATT&CK techniques
- **Coverage analysis**: Identify which techniques have detections and which are gaps
- **Threat intelligence**: Map observed adversary TTPs to ATT&CK for prioritized defense
- **Red team validation**: Use ATT&CK as a common language between offensive and defensive teams

---

## 8. Offensive Security

### Penetration Testing

| Phase | Activity |
|-------|----------|
| **Scoping** | Define targets, rules of engagement, success criteria, out-of-scope systems |
| **Reconnaissance** | OSINT, DNS enumeration, port scanning, service fingerprinting |
| **Vulnerability assessment** | Automated scanning + manual validation of findings |
| **Exploitation** | Attempt to exploit confirmed vulnerabilities to demonstrate impact |
| **Post-exploitation** | Lateral movement, privilege escalation, data access to show full impact chain |
| **Reporting** | Document findings with severity, evidence, reproduction steps, remediation guidance |

### Red, Blue, Purple Teams

| Team | Objective | Activities |
|------|-----------|------------|
| **Red** | Simulate real adversaries to test defenses | Adversary emulation, social engineering, physical security testing |
| **Blue** | Detect and respond to attacks | Monitoring, alerting, incident response, forensics |
| **Purple** | Maximize defensive improvement | Red team attacks while blue team observes; joint debrief to improve detection |

### Tabletop Exercises

- **Format**: Scenario-driven discussion (no live systems)
- **Participants**: Security, engineering, leadership, legal, communications
- **Scenarios**: Ransomware, data breach, supply chain compromise, insider threat
- **Output**: Gaps in playbooks, communication chains, decision authority
- **Cadence**: Quarterly for critical scenarios; annually for broader exercises

### Bug Bounty Programs

- **Scope definition**: Clear targets, out-of-scope areas, safe harbor policy
- **Severity tiers**: Critical/high/medium/low with corresponding payouts
- **Triage process**: Validate, deduplicate, assign to engineering teams
- **Response SLAs**: Acknowledge within 24h, triage within 72h, remediate based on severity
- **Relationship management**: Treat researchers as collaborators, not adversaries

---

## 9. Incident Response & Forensics

### Incident Response Lifecycle

```
  Prepare ──▶ Detect ──▶ Contain ──▶ Eradicate ──▶ Recover ──▶ Learn
     │           │           │            │             │           │
  Playbooks   Monitoring  Short-term   Remove root   Restore     Post-incident
  Training    Alerting    isolation    cause          services    review
  Tooling     Triage      Long-term    Patch/rebuild  Validate    Update controls
                          containment                 Monitor
```

### Severity Classification

| Level | Criteria | Response |
|-------|----------|----------|
| **Critical** | Active data breach, ransomware, complete service outage | Immediate war room, executive notification, external comms |
| **High** | Confirmed compromise with limited scope, significant service degradation | Escalate to Tier 2/3, containment within hours |
| **Medium** | Suspicious activity under investigation, minor service impact | Standard investigation workflow |
| **Low** | Policy violation, informational alert, no confirmed impact | Log, track, address in normal operations |

### Containment Strategies

| Strategy | When to Use |
|----------|-------------|
| **Network isolation** | Compromised host; prevent lateral movement |
| **Account disable** | Compromised credentials; block further access |
| **Service quarantine** | Affected service isolated from production traffic |
| **DNS sinkhole** | Redirect malicious domain traffic to controlled endpoint |
| **Snapshot and isolate** | Preserve evidence before remediation |

### Digital Forensics

| Phase | Activity | Principle |
|-------|----------|-----------|
| **Identification** | Determine scope and identify relevant evidence sources | Know what you're looking for |
| **Collection** | Acquire data with minimal alteration (disk images, memory dumps, log exports) | Preserve original evidence |
| **Chain of custody** | Document every transfer, access, and modification of evidence | Maintain admissibility |
| **Analysis** | Examine artifacts (file systems, memory, network captures, logs) | Follow the evidence |
| **Reporting** | Present findings with timeline, impact assessment, and attribution confidence | Clear, factual, defensible |

### Forensic Artifacts

| Source | What It Reveals |
|--------|----------------|
| **Disk image** | File access, deleted files, installed programs, user activity |
| **Memory dump** | Running processes, network connections, encryption keys, malware in memory |
| **Network capture** | Data exfiltration, C2 communication, lateral movement |
| **Event logs** | Authentication, process creation, privilege escalation, policy changes |
| **Cloud audit logs** | API calls, resource modifications, permission changes |

---

## 10. Security Awareness & Culture

### Training Program

| Audience | Focus | Cadence |
|----------|-------|---------|
| **All staff** | Phishing recognition, password hygiene, data handling, reporting | Annual + new hire onboarding |
| **Developers** | Secure coding, OWASP Top 10, dependency management, secrets handling | Quarterly or per-sprint security topics |
| **Operations** | Incident response, access management, patching, configuration management | Quarterly |
| **Leadership** | Risk governance, compliance obligations, incident decision authority | Annual + incident debriefs |

### Phishing Simulation

- **Baseline**: Run initial campaign to measure susceptibility rate
- **Progressive difficulty**: Increase sophistication over time (generic to targeted spear-phishing)
- **Immediate feedback**: Redirect users who click to training content explaining the indicators
- **Track trends**: Report rates by team/department; identify groups needing targeted training
- **No punishment**: Focus on improvement, not blame; high report rates are the goal

### Security Culture Indicators

| Healthy | Unhealthy |
|---------|-----------|
| Employees report suspicious emails proactively | Security is "someone else's job" |
| Developers ask about security implications during design | Security only surfaces during audits |
| Incidents are discussed openly as learning opportunities | Incidents are hidden or minimized |
| Security requirements are part of definition of done | Security review is a last-minute gate |
| Teams request threat modeling for new features | Threat models exist only as compliance artifacts |

### Human Factors

- **Social engineering**: Technical controls cannot fully prevent manipulation of people; training and verification procedures reduce risk
- **Cognitive load**: Too many security procedures cause workarounds; design security that integrates into workflows
- **Insider threat**: Combine technical controls (DLP, access monitoring) with organizational measures (separation of duties, access reviews)
- **Shadow IT**: Unmanaged tools and services bypass security controls; provide approved alternatives that meet user needs

---

> **Note**: For embedding security into the development pipeline (SAST, SCA, DAST, secure coding, supply chain, containers, secrets), see [DevSecOps](devsecops.md). For authentication and authorization architecture patterns, see [Architecture](architecture.md). For security standards and tools, see [Standards](../../_resources/standards.md) and [Tools](../../_resources/tools.md). For data-layer security, see [Data Security](../../data_management/concepts/core/security.md).
