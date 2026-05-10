# Enterprise Risk Management

**Scope**: Risk identification, analysis, response, monitoring, and governance at the project, program, and portfolio level in enterprise and regulated environments.

**Purpose**: Use this as a reference for building and operating risk management practices in complex enterprise environments. For foundational risk concepts (risk vs issue, basic response strategies), see [Project Management](project_management.md). For IT-specific technical and operational risks, see [ITPM](itpm.md). For cybersecurity risk frameworks, see [Cybersecurity](../../software_development/concepts/cybersecurity.md).

## Table of Contents

- [1. Risk Management Framework](#1-risk-management-framework)
- [2. Risk Identification](#2-risk-identification)
- [3. Risk Analysis](#3-risk-analysis)
- [4. Risk Response Planning](#4-risk-response-planning)
- [5. Risk Monitoring & Control](#5-risk-monitoring--control)
- [6. Risk Governance](#6-risk-governance)
- [7. Program & Portfolio Risk](#7-program--portfolio-risk)
- [8. Regulated Environment Risk Considerations](#8-regulated-environment-risk-considerations)
- [9. Risk Tools & Artifacts](#9-risk-tools--artifacts)
- [10. Risk Culture & Maturity](#10-risk-culture--maturity)

---

## 1. Risk Management Framework

### Risk Management Lifecycle

```
  Identify ──▶ Analyze ──▶ Plan ──▶ Implement ──▶ Monitor
      ▲                                              │
      └──────────── continuous reassessment ─────────┘
```

Risk management is not a phase. It runs continuously from project initiation through closing, with intensity and focus shifting across the lifecycle.

| Phase | Primary Risk Activity | Key Output |
|-------|----------------------|------------|
| **Initiation** | Identify landscape risks, feasibility unknowns, political constraints | Initial risk register, risk approach |
| **Planning** | Structured identification, qualitative and quantitative analysis, response planning | Prioritized risk register, contingency budget, risk response plans |
| **Execution** | Execute responses, monitor triggers, identify emerging risks | Updated register, issue escalations, risk reports |
| **Monitoring & Control** | Track KRIs, reassess probability and impact, audit response effectiveness | Trend analysis, variance reports, revised responses |
| **Closing** | Capture lessons learned, identify risk patterns for organizational knowledge | Risk retrospective, pattern library |

### Core Principles

1. **Risk is uncertainty that matters.** Not all uncertainty is risk. Risk is uncertainty that could materially affect objectives (scope, schedule, cost, quality, benefits).
2. **Risk appetite drives decisions.** Every organization has a tolerance for risk. Responses should align to that tolerance, not to the project team's personal comfort level.
3. **Proximity matters as much as severity.** A medium-impact risk due next week demands more attention than a high-impact risk 18 months away.
4. **Risk identification is everyone's job.** The PM owns the process; the team, stakeholders, and subject matter experts own the information.
5. **Positive risks exist.** Opportunities (positive risks) can be exploited, enhanced, or shared. Ignoring upside risk leaves value on the table.
6. **Over-managing risk is itself a risk.** Excessive risk governance consumes capacity. Scale risk process to the project's actual risk profile.

---

## 2. Risk Identification

### Identification Techniques

| Technique | When to Use | Strengths |
|-----------|-------------|-----------|
| **Brainstorming** | Early identification, team workshops | Broad coverage, team buy-in |
| **Checklist review** | All projects; use organizational risk taxonomy | Covers known categories, fast |
| **Lessons learned review** | Project kickoff, phase transitions | Avoids repeating past failures |
| **Assumption analysis** | Planning phase | Surfaces hidden risks behind assumed conditions |
| **SWOT analysis** | Strategic initiatives, program-level | Captures both threats and opportunities |
| **Pre-mortem** | Before execution begins | Imagine failure first, then trace causes |
| **Expert interviews** | Complex or unfamiliar domains | Deep insight into domain-specific risks |
| **Delphi technique** | When consensus is needed without groupthink | Anonymous expert input, iterative refinement |
| **Root cause analysis** | Post-incident, recurring issues | Identifies systemic risk sources |

### Enterprise Risk Categories

```
  Strategic ─── Operational ─── Financial ─── Compliance ─── Reputational
      │              │              │              │              │
  Alignment      Delivery        Budget        Regulatory     Public trust
  Benefits       Resources       Funding       Privacy        Media
  Political      Technology      Procurement   Accessibility  Stakeholder
  Direction      Dependencies    Contracts     Audit          Confidence
```

| Category | Examples |
|----------|----------|
| **Strategic** | Shifting political priorities, organizational restructuring, benefit realization failure, misalignment with enterprise strategy |
| **Operational** | Resource unavailability, technology failure, vendor dependency, process breakdown, knowledge loss |
| **Financial** | Budget cuts, funding delays, cost overruns, procurement challenges, fiscal year constraints |
| **Compliance** | Privacy legislation, accessibility standards, audit findings, regulatory changes, security standards |
| **Reputational** | Public trust erosion, media scrutiny, stakeholder confidence loss, service disruption visibility |
| **Technical** | Integration complexity, scalability limits, architecture risks, data quality, security vulnerabilities |
| **Organizational** | Change fatigue, skill gaps, cultural resistance, union considerations, staff turnover |

### Risk Statement Format

A well-formed risk statement separates cause, event, and effect:

**Because of** [cause/condition], **there is a risk that** [uncertain event] **which would result in** [impact on objectives].

Examples:
- Because of dependency on a legacy system with no vendor support, there is a risk that critical defects cannot be resolved within SLA, which would result in service disruption and schedule delay.
- Because of pending legislative changes to privacy requirements, there is a risk that data handling processes need mid-project redesign, which would result in scope expansion and budget pressure.

---

## 3. Risk Analysis

### Qualitative Analysis

Rate each risk on probability and impact using a consistent scale across the organization.

**Probability scale:**

| Rating | Label | Description |
|--------|-------|-------------|
| 1 | Rare | < 10% likelihood |
| 2 | Unlikely | 10-25% |
| 3 | Possible | 25-50% |
| 4 | Likely | 50-75% |
| 5 | Almost certain | > 75% |

**Impact scale:**

| Rating | Schedule | Cost | Scope | Quality |
|--------|----------|------|-------|---------|
| 1 - Negligible | < 1 week | < 5% | Minor feature affected | Cosmetic |
| 2 - Minor | 1-2 weeks | 5-10% | Secondary features | Workaround available |
| 3 - Moderate | 2-4 weeks | 10-20% | Significant functionality | Performance degradation |
| 4 - Major | 1-3 months | 20-40% | Core features | Major defects |
| 5 - Severe | > 3 months | > 40% | Project viability | Unusable deliverable |

### Risk Heat Map

```
  Impact
    5 │  5   10  [15] [20] [25]
    4 │  4    8  [12] [16] [20]
    3 │  3    6    9  [12] [15]
    2 │  2    4    6    8   10
    1 │  1    2    3    4    5
      └─────────────────────────
        1    2    3    4    5   Probability

  [ ] = High risk zone: requires active response plan
  Unbracketed = Moderate/low: monitor or accept
```

### Quantitative Analysis

Use when decisions involve significant financial exposure or schedule uncertainty.

| Method | What It Does | When to Use |
|--------|-------------|-------------|
| **Expected Monetary Value (EMV)** | Probability x impact in currency. Sum across risks for contingency budget | Budget planning, comparing alternatives |
| **Monte Carlo simulation** | Run thousands of scenarios to model schedule or cost probability distributions | Large programs, schedule confidence, go/no-go decisions |
| **Decision tree analysis** | Map decision paths with probabilities and payoffs | Build vs buy, vendor selection, technology choices |
| **Sensitivity analysis** | Identify which variables most affect outcomes (tornado diagram) | Prioritizing where to invest in risk reduction |
| **Three-point estimation** | Optimistic, most likely, pessimistic estimates for tasks | Schedule and cost estimation under uncertainty |

### Risk Scoring and Prioritization

Risk score = Probability x Impact x Proximity factor

| Proximity | Factor | Rationale |
|-----------|--------|-----------|
| Imminent (< 2 weeks) | 1.5x | Requires immediate action |
| Near-term (2-8 weeks) | 1.2x | Active monitoring and preparation |
| Mid-term (2-6 months) | 1.0x | Standard management |
| Long-term (> 6 months) | 0.8x | Monitor, reassess as it approaches |

---

## 4. Risk Response Planning

### Threat Responses (Negative Risks)

| Strategy | Description | Example | Cost Consideration |
|----------|-------------|---------|-------------------|
| **Avoid** | Eliminate the threat by changing the plan | Remove dependency on unsupported system | May narrow scope or delay |
| **Mitigate** | Reduce probability or impact | Add redundancy, prototype early, cross-train | Mitigation costs vs expected loss |
| **Transfer** | Shift consequences to a third party | Insurance, fixed-price contracts, SLAs with penalties | Transfer fees, residual risk remains |
| **Accept (active)** | Acknowledge and prepare a contingency plan | Contingency budget, fallback approach documented | Contingency reserve cost |
| **Accept (passive)** | Acknowledge and take no action unless it occurs | Document and monitor | Zero upfront cost, full exposure |
| **Escalate** | Risk is beyond the project's authority or capacity | Escalate to program, portfolio, or executive level | Depends on organizational response |

### Opportunity Responses (Positive Risks)

| Strategy | Description | Example |
|----------|-------------|---------|
| **Exploit** | Ensure the opportunity is realized | Assign best resources to capture early delivery benefit |
| **Enhance** | Increase probability or impact | Invest in capability that amplifies the upside |
| **Share** | Allocate ownership to a party better positioned to capture it | Joint venture, partnership, shared platform |
| **Accept** | Welcome the opportunity if it occurs but don't actively pursue | No additional investment |

### Response Planning Principles

- **One owner per risk**: Shared ownership means no ownership. Assign a single person accountable for monitoring and response.
- **Trigger conditions**: Define specific, observable conditions that activate the response plan. Vague triggers produce late responses.
- **Secondary risks**: Every response can introduce new risks. Evaluate secondary risks before committing to a response.
- **Residual risk**: After applying a response, assess what risk remains. If residual risk exceeds tolerance, layer additional responses.
- **Contingency vs management reserve**: Contingency covers known risks with planned responses. Management reserve covers unknown risks (unknowns). Both need budget allocation.

### Contingency Budget Estimation

| Method | Approach |
|--------|----------|
| **EMV-based** | Sum of (probability x impact) for all active risks |
| **Percentage of budget** | 5-15% depending on project complexity and organizational norms |
| **Simulation-based** | Monte Carlo output at desired confidence level (P75 or P80) |
| **Hybrid** | EMV for known risks + percentage buffer for unknown risks |

---

## 5. Risk Monitoring & Control

### Key Risk Indicators (KRIs)

KRIs are leading indicators that signal when risk exposure is changing before impact materializes.

| KRI | What It Signals |
|-----|----------------|
| Schedule variance trending negative | Delivery risk increasing |
| Burn rate exceeding plan | Cost overrun risk |
| Requirements change frequency increasing | Scope creep |
| Staff turnover above baseline | Knowledge loss, capacity risk |
| Vendor response times degrading | Dependency risk |
| Defect escape rate rising | Quality risk |
| Stakeholder engagement declining | Alignment risk, change resistance |
| Compliance findings accumulating | Regulatory risk |
| Contingency budget consumption rate | Overall risk exposure |

### Risk Review Cadence

| Project Size/Risk | Frequency | Participants |
|-------------------|-----------|--------------|
| Small, low risk | Monthly risk review in team meeting | PM, team leads |
| Medium complexity | Bi-weekly dedicated risk review | PM, team leads, key stakeholders |
| Large, high risk | Weekly risk review + monthly risk board | PM, sponsors, risk owners, steering committee |
| Program/portfolio | Monthly risk aggregation + quarterly executive review | Program manager, PMO, executive sponsors |

### Risk Reporting

| Audience | Content | Format |
|----------|---------|--------|
| **Project team** | Full risk register, trigger status, action items | Detailed register, standup discussion |
| **Steering committee** | Top 5-10 risks, trend direction, decisions needed | Heat map, risk dashboard, brief narrative |
| **Executive/portfolio** | Aggregated risk posture, cross-project dependencies, budget exposure | Executive summary, portfolio risk dashboard |
| **Audit/oversight** | Risk process compliance, response effectiveness, residual risk | Compliance report, evidence trail |

### Risk Trend Tracking

Track each risk's trajectory over time, not just its current score.

| Trend | Indicator | Action |
|-------|-----------|--------|
| Increasing | Score rising or approaching trigger | Escalate, strengthen response, increase monitoring |
| Stable | Score unchanged, within tolerance | Continue current approach |
| Decreasing | Score dropping, trigger conditions receding | Consider reducing monitoring intensity |
| Realized | Risk has occurred, now an issue | Activate contingency, transition to issue management |
| Closed | Risk is no longer possible or relevant | Archive with rationale |

---

## 6. Risk Governance

### Risk Governance Structure

```
  Board / Executive Committee
  ────────────────────────────
  Sets risk appetite, approves risk policy, reviews portfolio risk posture

        ▼
  Risk Committee / PMO
  ────────────────────
  Defines risk standards, aggregates risk across projects, escalation authority

        ▼
  Program Manager
  ────────────────
  Manages cross-project risks, dependency risks, resource risks

        ▼
  Project Manager
  ────────────────
  Owns project risk register, executes risk process, escalates beyond authority
```

### Risk Appetite and Tolerance

| Concept | Definition | Example |
|---------|------------|---------|
| **Risk appetite** | Amount of risk an organization is willing to accept in pursuit of objectives | "We accept moderate schedule risk for innovation projects but zero tolerance for privacy breaches" |
| **Risk tolerance** | Acceptable variation around objectives | "+/- 10% cost variance, no more than 2 weeks schedule slip per phase" |
| **Risk threshold** | Specific level at which risk requires escalation or different action | "Any risk scored 20+ requires steering committee review" |
| **Risk capacity** | Maximum risk an organization can absorb | Determined by financial reserves, operational resilience, regulatory constraints |

### Escalation Criteria

| Condition | Escalation Target |
|-----------|-------------------|
| Risk exceeds project manager's authority to respond | Program manager or sponsor |
| Risk affects multiple projects or programs | PMO or portfolio level |
| Risk involves regulatory, legal, or compliance exposure | Legal, compliance, or risk committee |
| Risk threatens organizational reputation or public trust | Executive leadership, communications |
| Financial exposure exceeds project contingency | Sponsor, finance, management reserve holder |

---

## 7. Program & Portfolio Risk

### Aggregation Challenges

Project-level risk registers do not simply sum to program risk. Aggregation requires identifying:

- **Correlated risks**: The same event (e.g., vendor failure) affects multiple projects simultaneously
- **Dependency risks**: Project A's deliverable is Project B's dependency; delay cascades
- **Resource contention**: Shared resources create hidden coupling between projects
- **Systemic risks**: Organization-wide events (budget cuts, reorganization, policy changes) affect all projects

### Portfolio Risk Dashboard

| Dimension | Metrics |
|-----------|---------|
| **Concentration** | How many projects share the same risk category or vendor |
| **Correlation** | Which risks would cascade across multiple projects if realized |
| **Capacity** | Aggregate contingency vs aggregate exposure |
| **Velocity** | How quickly risks are being identified and closed |
| **Trend** | Portfolio risk posture improving, stable, or deteriorating |

### Cross-Project Dependencies

```
  Project A ──deliverable──▶ Project B ──integration──▶ Project C
                                │
                          delay cascades
                                │
                                ▼
                     schedule risk compounds
                     across the portfolio
```

- **Map dependencies explicitly**: Maintain a dependency register alongside the risk register
- **Assign dependency owners**: Each dependency has an owner in both the upstream and downstream project
- **Track dependency health**: Green (on track), yellow (at risk), red (delayed/blocked)
- **Buffer at integration points**: Schedule buffers where projects connect, not just within projects

---

## 8. Regulated Environment Risk Considerations

### Political and Governance Risks

| Risk | Consideration |
|------|---------------|
| **Leadership transitions** | Executive or board transitions can shift priorities, pause projects, or reassign funding |
| **Policy direction changes** | New strategic direction can redefine project scope mid-delivery |
| **Legislative and regulatory mandates** | New legislation or regulations can impose requirements that reshape timelines and budgets |
| **Oversight bodies** | Auditor, privacy commissioner, ombudsman, or regulator findings can trigger scope changes |
| **Cross-departmental dependencies** | Multi-department initiatives face coordination complexity and competing priorities |

### Regulatory and Compliance Risks

| Domain | Requirements |
|--------|-------------|
| **Privacy** | Applicable privacy legislation, Privacy Impact Assessments (PIAs) for systems handling personal information |
| **Accessibility** | Compliance with accessibility standards (WCAG 2.1 AA) for digital services |
| **Language requirements** | Bilingual or multilingual requirements; translation timelines affect schedule |
| **Records management** | Retention schedules, information request readiness, information management policies |
| **Security** | Security classifications, security assessments, Authority to Operate (ATO) |

### Procurement and Financial Risks

- **Fiscal year boundaries**: Funding lapses if not spent within fiscal year; creates artificial schedule pressure
- **Procurement cycles**: Formal procurement processes are lengthy; lead times of 3-6+ months are common in regulated environments
- **Sole-source limitations**: Thresholds and justification requirements limit flexibility
- **Multi-year funding**: Projects spanning fiscal years face annual re-approval and potential reductions
- **Transfer payment agreements**: Funding to external entities involves additional oversight, reporting, and audit requirements

### Accountability Risks

- **Transparency obligations**: Information requests can expose project documents, emails, and decisions
- **External scrutiny**: High-profile projects face media or stakeholder attention; failures are highly visible
- **Executive accountability**: Senior leaders are accountable for project outcomes; this shapes risk tolerance and decision-making speed
- **Audit readiness**: Maintain decision trails, approvals, and rationale documentation at all times
- **Community engagement**: Consultation requirements for projects affecting communities or stakeholder groups

### Organizational and Change Risks

| Risk | Regulated Environment Context |
|------|-------------------------------|
| **Change resistance** | Unionized or long-tenure environments with established processes create inertia |
| **Staff mobility** | Secondments, acting assignments, and transfers create knowledge loss mid-project |
| **Decision authority** | Multiple approval layers extend decision timelines |
| **Capacity constraints** | Competing with private sector for technical talent; lengthy hiring processes |
| **Transformation fatigue** | Multiple concurrent modernization initiatives compete for the same organizational capacity |

---

## 9. Risk Tools & Artifacts

### Risk Register Design

| Field | Purpose |
|-------|---------|
| **ID** | Unique identifier for tracking and reference |
| **Risk statement** | Structured: cause, event, effect |
| **Category** | Strategic, operational, financial, compliance, technical, reputational |
| **Probability** | 1-5 rating |
| **Impact** | 1-5 rating across relevant dimensions (schedule, cost, scope, quality) |
| **Score** | Probability x highest impact |
| **Proximity** | When the risk could materialize |
| **Response strategy** | Avoid, mitigate, transfer, accept, escalate |
| **Response actions** | Specific actions with owners and deadlines |
| **Trigger conditions** | Observable conditions that activate the response |
| **Owner** | Single person accountable |
| **Status** | Open, in progress, realized (now an issue), closed |
| **Trend** | Increasing, stable, decreasing |
| **Last reviewed** | Date of most recent assessment |

### Risk Assessment Matrix (RACI for Risk)

| Activity | PM | Risk Owner | Sponsor | Steering Committee | PMO |
|----------|-----|-----------|---------|-------------------|-----|
| Identify risks | A/R | R | C | I | C |
| Analyze and score | A/R | R | I | I | C |
| Plan responses | A | R | C | I | I |
| Execute responses | I | A/R | I | I | I |
| Monitor and report | A/R | R | I | C | C |
| Escalate | R | C | A | A | C |

*R = Responsible, A = Accountable, C = Consulted, I = Informed*

### Risk Workshop Facilitation

1. **Preparation**: Distribute risk categories, historical lessons learned, and project context in advance
2. **Structured brainstorm**: Walk through each risk category systematically; use "because of / risk that / resulting in" format
3. **Quick scoring**: Probability and impact for each risk using dot voting or consensus
4. **Response assignment**: Assign owner and initial response strategy for high-priority risks
5. **Documentation**: Capture in risk register within 24 hours; circulate for validation
6. **Duration**: 90-120 minutes for initial workshop; 30-45 minutes for periodic reviews

---

## 10. Risk Culture & Maturity

### Risk Maturity Model

| Level | Characteristics | Indicators |
|-------|----------------|------------|
| **1 - Ad hoc** | Risk managed reactively; no standard process | No risk register, risks discussed only when issues arise |
| **2 - Initial** | Risk register exists but inconsistently maintained | Register created at kickoff, rarely updated, one person's responsibility |
| **3 - Defined** | Standard risk process applied across projects | Regular reviews, consistent scoring, response plans documented |
| **4 - Managed** | Quantitative analysis, KRIs, risk-informed decisions | Monte Carlo used for major decisions, KRIs tracked, risk trends reported |
| **5 - Optimized** | Organizational learning, predictive risk intelligence | Lessons learned systematically applied, risk patterns identified and shared across portfolio |

### Building Risk Culture

| Behavior | Enables |
|----------|---------|
| Leaders ask "what could go wrong?" in every review | Normalizes risk identification |
| Risk identification is rewarded, not punished | Psychological safety to surface bad news early |
| Risk discussions are forward-looking, not blame-focused | Team engagement in risk process |
| Contingency budgets are standard, not exceptional | Realistic planning |
| Lessons learned are captured and applied to future projects | Organizational learning |
| Risk owners have authority to act, not just report | Effective response execution |

### Common Anti-Patterns

| Anti-Pattern | Consequence | Fix |
|-------------|-------------|-----|
| Risk register as compliance artifact | Created at kickoff, never updated, no value | Integrate risk review into regular team cadence |
| "Green until red" reporting | Risks hidden until they become issues; no early warning | Track trends, reward early escalation |
| Optimism bias in scoring | Systematic underestimation of probability and impact | Use reference class data, calibrate with historical actuals |
| Risk owner without authority | Owner can report but not act; responses stall | Align authority to accountability |
| All risks treated equally | High-severity risks get same attention as low-severity | Focus attention on the top 10; accept the tail |
| Risk process scaled to the largest project | Small projects burdened with heavy governance | Scale risk process to project size and risk profile |

---

> **Note**: For foundational project management concepts (risk vs issue, basic response strategies), see [Project Management](project_management.md). For IT-specific technical and operational risks, see [ITPM](itpm.md). For cybersecurity risk frameworks (NIST CSF, ISO 27001), see [Cybersecurity](../../software_development/concepts/cybersecurity.md). For risk as a cross-cutting thread in the project lifecycle, see [Systems Thinking](../strategy/systems_thinking.md).
