# Business & Financial Practices for IT/PM

**Scope**: Business case development, investment appraisal, cost estimation, financial governance, earned value management, benefits realization, procurement, contract management, and IT financial management in enterprise and regulated environments.

**Purpose**: Use this as a reference for financial planning, justification, and control across IT and project management. For foundational budget and cost concepts, see [Project Management](project_management.md). For financial reporting formats, see [Reporting](reporting.md). For enterprise risk including financial risk, see [Risk Management](risk_management.md).

## Table of Contents

- [1. Business Case Development](#1-business-case-development)
- [2. Investment Appraisal](#2-investment-appraisal)
- [3. Cost Estimation](#3-cost-estimation)
- [4. CapEx, OpEx, and Cloud Economics](#4-capex-opex-and-cloud-economics)
- [5. Earned Value Management](#5-earned-value-management)
- [6. Benefits Realization Management](#6-benefits-realization-management)
- [7. Financial Governance & Controls](#7-financial-governance--controls)
- [8. Procurement & Contract Management](#8-procurement--contract-management)
- [9. IT Financial Management & FinOps](#9-it-financial-management--finops)
- [10. Regulated & Institutional Financial Context](#10-regulated--institutional-financial-context)

---

## 1. Business Case Development

### Business Case Structure

| Section | Content |
|---------|---------|
| **Executive summary** | Problem, recommended option, expected benefits, cost, timeline |
| **Strategic alignment** | How the initiative supports organizational strategy and mandate |
| **Problem statement** | Current state, pain points, cost of inaction |
| **Options analysis** | Status quo + 2-3 viable alternatives with pros, cons, cost, risk |
| **Recommended option** | Rationale for selection, trade-offs accepted |
| **Cost-benefit analysis** | Costs (one-time and recurring), quantified benefits, intangible benefits |
| **Risk assessment** | Key risks, mitigation strategies, residual risk |
| **Implementation approach** | High-level timeline, phases, resource requirements |
| **Assumptions and constraints** | Conditions the case depends on; limitations |
| **Success criteria** | How success will be measured and when |

### Options Analysis

Every business case should evaluate a minimum of three options:

| Option | Purpose |
|--------|---------|
| **Status quo** | Baseline. What happens if we do nothing? Quantify the cost of inaction |
| **Minimum viable** | Lowest investment that addresses the core problem |
| **Recommended** | Best balance of cost, risk, benefit, and strategic fit |
| **Aspirational** (optional) | Full scope if budget and risk tolerance allow |

For each option, document: total cost, timeline, benefits (quantified and qualitative), risks, dependencies, and organizational impact.

### Cost-Benefit Analysis

| Component | Include |
|-----------|---------|
| **One-time costs** | Hardware, software licenses, implementation services, migration, training, change management |
| **Recurring costs** | Licenses/subscriptions, support, maintenance, hosting, staffing, operations |
| **Direct benefits** | Cost savings, revenue increase, productivity gains, FTE reduction/reallocation |
| **Indirect benefits** | Risk reduction, improved decision-making, compliance, citizen/customer satisfaction |
| **Intangible benefits** | Staff morale, organizational reputation, strategic positioning |
| **Cost of inaction** | Growing maintenance costs, missed opportunities, compliance risk, competitive disadvantage |

### Business Case Anti-Patterns

| Anti-Pattern | Problem |
|-------------|---------|
| Benefits without baseline | Cannot measure improvement without a measured starting point |
| One option presented | No comparison means no real decision; stakeholders suspect the conclusion is predetermined |
| Costs without lifecycle | Showing implementation cost but hiding 5-year operational cost misleads approvers |
| Optimistic-only scenario | No sensitivity analysis; case collapses when assumptions prove wrong |
| Technology-led justification | "We need this platform" instead of "this problem costs X and the solution returns Y" |

---

## 2. Investment Appraisal

### Appraisal Methods

| Method | Formula / Approach | Use When |
|--------|-------------------|----------|
| **ROI** | (Net benefit / Cost) x 100 | Quick comparison of alternatives; widely understood |
| **NPV** | Sum of [Cash flow / (1+r)^n] for each period | Comparing investments with different timelines; accounts for time value of money |
| **IRR** | Discount rate where NPV = 0 | Comparing projects to a hurdle rate; useful when capital is constrained |
| **Payback period** | Time until cumulative benefits equal cumulative costs | Cash-flow-sensitive decisions; risk-averse environments |
| **BCR** | Total discounted benefits / Total discounted costs | Non-revenue environments; ratio > 1.0 means benefits exceed costs |

### NPV Worked Example

```
  Year 0: -500,000  (initial investment)
  Year 1: +120,000  (net benefit)
  Year 2: +150,000
  Year 3: +180,000
  Year 4: +200,000
  Year 5: +200,000

  Discount rate: 5%

  NPV = -500,000
      + 120,000/1.05^1  = +114,286
      + 150,000/1.05^2  = +136,054
      + 180,000/1.05^3  = +155,490
      + 200,000/1.05^4  = +164,540
      + 200,000/1.05^5  = +156,705

  NPV = +227,075  (positive = investment justified at 5% discount rate)
  Payback period: between Year 3 and Year 4
```

### Sensitivity Analysis

Test how outcomes change when assumptions vary. Identify which variables the decision is most sensitive to.

| Variable | Base Case | Pessimistic | Optimistic |
|----------|-----------|-------------|------------|
| Implementation cost | 500K | 650K (+30%) | 450K (-10%) |
| Annual benefit | 170K avg | 120K (-30%) | 200K (+18%) |
| Timeline to value | 6 months | 12 months | 3 months |
| Discount rate | 5% | 8% | 3% |
| **NPV** | **+227K** | **-45K** | **+380K** |

If NPV goes negative in realistic scenarios, the investment is fragile.

---

## 3. Cost Estimation

### Estimation Techniques

| Technique | Approach | Accuracy | Best For |
|-----------|----------|----------|----------|
| **Analogous** | Based on similar past projects | +/- 25-50% | Early estimates, limited detail |
| **Parametric** | Statistical model (cost per unit x quantity) | +/- 15-25% | Repeatable work, historical data available |
| **Bottom-up** | Sum of detailed work package estimates | +/- 5-15% | Detailed planning, well-understood scope |
| **Three-point** | (Optimistic + 4x Most Likely + Pessimistic) / 6 | +/- 10-20% | Uncertain tasks, risk-aware estimation |
| **Expert judgment** | SME consensus (Delphi, planning poker) | Varies | Novel work, limited historical data |
| **Vendor quotes** | Formal pricing from suppliers | Depends on SOW clarity | Procured components, outsourced work |

### Estimation Principles

- **Estimate ranges, not points**: A single number creates false precision. Provide optimistic/likely/pessimistic.
- **Separate known from unknown**: Estimate what you know; add contingency for what you don't.
- **Estimate effort, then duration**: Effort (person-days) is more stable than calendar time.
- **Account for non-project time**: Meetings, admin, context-switching, leave. Productive capacity is typically 60-75% of available hours.
- **Re-estimate as scope clarifies**: Initial estimates are guesses. Update estimates at phase transitions.
- **Document assumptions**: Every estimate rests on assumptions. List them so the estimate can be re-evaluated when assumptions change.

### Common Estimation Biases

| Bias | Effect | Countermeasure |
|------|--------|----------------|
| **Optimism bias** | Systematic underestimation of cost and duration | Use reference class data from past projects |
| **Anchoring** | First number heard dominates all subsequent estimates | Estimate independently before sharing |
| **Scope exclusion** | Forgetting testing, documentation, deployment, training | Use a checklist of standard activities |
| **Happy path** | Estimating only the "everything goes right" scenario | Three-point estimation; pre-mortem |
| **Precision bias** | Reporting "127 days" when accuracy is +/- 30% | Express as ranges; match precision to accuracy |

---

## 4. CapEx, OpEx, and Cloud Economics

### CapEx vs OpEx

| Dimension | CapEx (Capital) | OpEx (Operational) |
|-----------|----------------|-------------------|
| **Nature** | One-time investment in long-lived assets | Ongoing operational expenses |
| **Examples** | Server hardware, data center, perpetual licenses, major development | Cloud subscriptions, SaaS fees, managed services, support contracts |
| **Accounting** | Capitalized on balance sheet, depreciated over useful life | Expensed in the period incurred |
| **Budget cycle** | Requires capital budget approval; often annual | Funded from operating budget; more flexible |
| **Cash flow** | Large upfront outlay, no recurring cost | Smaller recurring payments, predictable spend |
| **Scalability** | Fixed capacity; scaling requires new purchase | Elastic; scale up/down with demand |

### Cloud Shift: CapEx to OpEx

The shift to cloud services converts traditionally capital-intensive IT spending into operational expenses. This has financial planning implications:

| Impact Area | Implication |
|-------------|-------------|
| **Budget structure** | Shifts from large capital requests to ongoing operating budget line items |
| **Forecasting** | Requires consumption-based forecasting rather than asset-based depreciation schedules |
| **Cost variability** | Costs vary with usage; requires monitoring and controls to prevent overruns |
| **Approval process** | May bypass capital approval thresholds but requires operating budget capacity |
| **Financial reporting** | Changes how IT costs appear on financial statements |
| **Vendor lock-in** | Ongoing dependency creates switching costs that don't appear in TCO unless explicitly modeled |

### Total Cost of Ownership (TCO)

A TCO analysis captures the full lifecycle cost, not just the purchase price.

| Cost Layer | Components |
|------------|-----------|
| **Acquisition** | Hardware, software licenses, implementation services, migration, initial training |
| **Operations** | Hosting, support, maintenance, patching, monitoring, staffing |
| **Administration** | License management, vendor management, compliance, auditing |
| **Change** | Upgrades, enhancements, integrations, re-training |
| **Decommission** | Data migration, contract exit, archive, disposal |
| **Hidden costs** | Downtime, productivity loss during transition, shadow IT, opportunity cost |

### TCO Comparison Framework

| Factor | On-Premises | Cloud/SaaS |
|--------|-------------|------------|
| **Upfront** | High (hardware, licenses, build-out) | Low (subscription start) |
| **Year 1-3** | Moderate (operations, staffing) | Moderate (subscriptions, ops) |
| **Year 4-7** | Increasing (refresh, end-of-life, tech debt) | Stable (but compounding subscription cost) |
| **Flexibility** | Low (committed assets) | High (scale, pivot, cancel) |
| **Exit cost** | Hardware disposal, data migration | Data extraction, contract termination, replatforming |

---

## 5. Earned Value Management

### Core Metrics

| Metric | Formula | Meaning |
|--------|---------|---------|
| **PV** (Planned Value) | Budgeted cost of work scheduled | What you planned to spend by now |
| **EV** (Earned Value) | Budgeted cost of work performed | What you've actually accomplished (in budget terms) |
| **AC** (Actual Cost) | Actual cost of work performed | What you've actually spent |

### Variance Indicators

| Indicator | Formula | Interpretation |
|-----------|---------|---------------|
| **CV** (Cost Variance) | EV - AC | Positive = under budget. Negative = over budget |
| **SV** (Schedule Variance) | EV - PV | Positive = ahead of schedule. Negative = behind |
| **CPI** (Cost Performance Index) | EV / AC | > 1.0 = efficient. < 1.0 = over budget |
| **SPI** (Schedule Performance Index) | EV / PV | > 1.0 = ahead. < 1.0 = behind schedule |

### Forecasting

| Forecast | Formula | Use When |
|----------|---------|----------|
| **EAC** (Estimate at Completion) | BAC / CPI | Current cost performance will continue |
| **EAC** (atypical variance) | AC + (BAC - EV) | Variance was a one-time event |
| **EAC** (both cost and schedule) | AC + [(BAC - EV) / (CPI x SPI)] | Both cost and schedule pressures will continue |
| **ETC** (Estimate to Complete) | EAC - AC | Remaining cost from current point |
| **VAC** (Variance at Completion) | BAC - EAC | Expected budget variance at project end |
| **TCPI** (To-Complete Performance Index) | (BAC - EV) / (BAC - AC) | Required efficiency to finish on budget |

### EVM Interpretation Quick Reference

```
  CPI > 1 and SPI > 1  →  Under budget and ahead of schedule (ideal)
  CPI > 1 and SPI < 1  →  Under budget but behind schedule
  CPI < 1 and SPI > 1  →  Over budget but ahead of schedule
  CPI < 1 and SPI < 1  →  Over budget and behind schedule (intervention needed)

  TCPI > 1.0  →  Must improve efficiency to finish on budget
  TCPI = 1.0  →  On track
  TCPI < 1.0  →  Can afford some inefficiency and still finish on budget
```

### EVM Limitations

- Requires a reliable baseline and consistent progress measurement
- Works best with fixed-scope projects; harder to apply in highly iterative/Agile environments
- Measures schedule performance in cost terms, not calendar time
- Does not capture quality, stakeholder satisfaction, or business value
- Can incentivize reporting work as "complete" prematurely to improve EV

---

## 6. Benefits Realization Management

### Benefits Lifecycle

```
  Identify ──▶ Quantify ──▶ Plan ──▶ Track ──▶ Realize ──▶ Sustain
     │             │          │         │           │           │
  Business     Baseline    Owners    Measure     Validate    Embed in
  case         metrics     assigned  progress    achieved    operations
```

### Benefits Categories

| Category | Examples | Measurement |
|----------|----------|-------------|
| **Financial** | Cost savings, revenue increase, cost avoidance | Currency, percentage reduction |
| **Efficiency** | Time saved, FTE reallocation, cycle time reduction | Hours, FTE, processing time |
| **Quality** | Error reduction, defect rate, customer satisfaction | Rate, score, percentage |
| **Compliance** | Regulatory adherence, audit findings reduction | Findings count, compliance rate |
| **Strategic** | Market position, capability enablement, innovation | Capability maturity, strategic milestone |
| **Risk reduction** | Reduced exposure, improved resilience, security posture | Risk score, incident frequency |

### Benefits Register

| Field | Purpose |
|-------|---------|
| **Benefit ID** | Unique identifier |
| **Description** | What the benefit is and who receives it |
| **Category** | Financial, efficiency, quality, compliance, strategic, risk |
| **Baseline** | Current state measurement before the initiative |
| **Target** | Expected improved state with timeline |
| **Measurement method** | How the benefit will be measured (data source, frequency) |
| **Owner** | Business owner accountable for realizing the benefit |
| **Dependencies** | What must happen for this benefit to be realized |
| **Status** | Projected, in progress, realized, at risk, not realized |

### Benefits Realization Principles

- **Baseline before you start**: You cannot demonstrate improvement without measuring the starting point.
- **Business owns benefits**: The project team delivers capability; the business realizes benefits. Assign business owners.
- **Benefits lag delivery**: Most benefits take 6-18 months after deployment to fully materialize. Plan measurement accordingly.
- **Track through transition**: Benefits tracking should survive project closure. Hand off to operations or a benefits management function.
- **Dis-benefits exist**: Some initiatives create negative side effects (increased complexity, transition disruption, staff displacement). Acknowledge and manage them.

---

## 7. Financial Governance & Controls

### Budget Lifecycle

```
  Develop ──▶ Submit ──▶ Approve ──▶ Allocate ──▶ Execute ──▶ Report ──▶ Close
     │           │           │           │            │           │          │
  Estimate    Business    Authority   Fund codes   Spend      Variance   Reconcile
  options     case        approval    cost centers  control    analysis   audit
```

### Delegated Financial Authority

| Authority Level | Typical Threshold | Approval Scope |
|-----------------|-------------------|----------------|
| **Project manager** | Up to 10-25K | Minor scope changes, operational expenses within approved budget |
| **Director** | Up to 50-100K | Budget reallocation within program, minor procurements |
| **VP / Senior executive** | Up to 250K-1M | New initiatives, significant procurements, contract awards |
| **CFO / COO** | Up to 5-10M | Major programs, enterprise investments |
| **Board / Executive committee** | Above threshold | Enterprise-scale investments, multi-year commitments |

*Thresholds vary by organization. Highly regulated and accountability-driven environments typically enforce more granular thresholds.*

### Financial Controls

| Control | Purpose |
|---------|---------|
| **Budget baseline** | Approved budget used as reference; changes require formal approval |
| **Variance thresholds** | Define acceptable variance (e.g., +/- 10%); breaches trigger escalation |
| **Commitment tracking** | Track committed spend (POs, contracts) vs available budget |
| **Accrual management** | Recognize expenses when incurred, not when paid; aligns cost with work performed |
| **Segregation of duties** | Separate budget approval, procurement, and payment authorization |
| **Audit trail** | All financial transactions documented, traceable, and reviewable |
| **Period-end reporting** | Monthly financial close; reconcile actuals to budget |

### Contingency and Management Reserve

| Type | Owned By | Purpose | Sizing |
|------|----------|---------|--------|
| **Contingency reserve** | Project manager | Known risks with planned responses | EMV-based or percentage (5-15%) |
| **Management reserve** | Sponsor / executive | Unknown risks (unknown unknowns) | Percentage of total budget (5-10%) |

Contingency is part of the project baseline. Management reserve sits outside the baseline and requires sponsor approval to access.

---

## 8. Procurement & Contract Management

### Contract Types

| Type | Risk Allocation | Best For |
|------|----------------|----------|
| **Firm Fixed Price (FFP)** | Seller bears cost risk | Well-defined scope, clear requirements |
| **Fixed Price Incentive (FPI)** | Shared via incentive/penalty | Defined scope with performance targets |
| **Time and Materials (T&M)** | Buyer bears cost risk | Unclear scope, staff augmentation, advisory |
| **Cost Plus Fixed Fee (CPFF)** | Buyer bears cost risk + fixed fee | R&D, uncertain scope, trusted vendor |
| **Cost Plus Incentive Fee (CPIF)** | Shared via incentive structure | Large programs with evolving requirements |

```
  Risk to Buyer:   Low ◀─────────────────────────────────▶ High

  Contract Type:   FFP ── FPI ── T&M ── CPFF ── CPIF

  Risk to Seller:  High ◀────────────────────────────────▶ Low
```

### Procurement Process

| Phase | Activities | Key Outputs |
|-------|-----------|-------------|
| **Plan** | Needs analysis, make-or-buy decision, procurement strategy, market research | Procurement plan, acquisition strategy |
| **Solicit** | Draft RFP/RFQ/RFI, define evaluation criteria, issue to market | RFP document, Q&A responses |
| **Evaluate** | Score proposals, conduct demos/interviews, reference checks, negotiate | Evaluation matrix, shortlist, recommendation |
| **Award** | Select vendor, finalize contract, obtain approvals | Signed contract, SOW, SLAs |
| **Manage** | Monitor deliverables, track SLAs, manage changes, conduct reviews | Status reports, performance reviews, change orders |
| **Close** | Final acceptance, lessons learned, contract closure, transition | Closeout report, asset transfer |

### Evaluation Criteria

| Criterion | Weight (typical) | Assessment |
|-----------|-------------------|------------|
| **Technical capability** | 30-40% | Solution fit, architecture, scalability, integration |
| **Experience and references** | 15-20% | Similar projects, client references, case studies |
| **Team qualifications** | 10-15% | Key personnel, certifications, domain expertise |
| **Cost** | 20-30% | Total cost (not just unit price), TCO, pricing model |
| **Risk** | 5-10% | Vendor stability, transition risk, lock-in, exit provisions |
| **Innovation** | 5-10% | Value-added approaches, continuous improvement commitments |

### SLA Management

| SLA Component | Definition |
|---------------|-----------|
| **Service levels** | Specific, measurable performance targets (uptime, response time, resolution time) |
| **Measurement method** | How performance is measured (monitoring tools, reporting frequency) |
| **Reporting** | Frequency and format of performance reports |
| **Remedies** | Service credits, penalties, escalation for breaches |
| **Exclusions** | Planned maintenance, force majeure, customer-caused issues |
| **Review cadence** | Periodic SLA review and adjustment (annual minimum) |

### Vendor Relationship Management

| Relationship Type | Characteristics | Management Approach |
|-------------------|----------------|-------------------|
| **Transactional** | Commodity purchase, low switching cost | Competitive bidding, minimal management |
| **Operational** | Ongoing service, moderate dependency | Regular reviews, SLA tracking, performance metrics |
| **Strategic** | Deep integration, high dependency, co-innovation | Executive sponsorship, joint roadmap, shared risk/reward |

---

## 9. IT Financial Management & FinOps

### IT Cost Models

| Model | Mechanism | Best For |
|-------|-----------|----------|
| **Cost center** | IT costs absorbed centrally; no allocation to business units | Small organizations, shared infrastructure |
| **Chargeback** | IT costs allocated to business units based on actual consumption | Cost accountability, consumption-based budgeting |
| **Showback** | IT costs reported to business units without actual charge | Awareness building, pre-chargeback transition |
| **Subscription** | Business units pay fixed rates for IT service tiers | Predictable budgeting, service catalog model |

### FinOps (Cloud Financial Operations)

FinOps is the practice of bringing financial accountability to cloud spending.

```
  Inform ──▶ Optimize ──▶ Operate
     │            │            │
  Visibility   Right-size   Governance
  Allocation   Reservations Policies
  Forecasting  Waste removal Automation
```

| Phase | Activities |
|-------|-----------|
| **Inform** | Tag resources for cost allocation, build dashboards, report by team/service/environment, forecast trends |
| **Optimize** | Right-size instances, purchase reserved capacity, eliminate idle resources, use spot/preemptible where appropriate |
| **Operate** | Set budgets and alerts, automate scaling policies, enforce tagging standards, review anomalies |

### Cloud Cost Optimization Levers

| Lever | Typical Savings |
|-------|----------------|
| **Right-sizing** | 20-40% on compute; most workloads are over-provisioned |
| **Reserved instances / savings plans** | 30-60% vs on-demand for predictable workloads |
| **Spot / preemptible instances** | 60-90% for fault-tolerant, interruptible workloads |
| **Storage tiering** | 30-70% by moving cold data to archive tiers |
| **Idle resource cleanup** | Variable; non-production environments left running are common |
| **Architecture optimization** | Variable; serverless, caching, and query optimization reduce consumption |

### IT Service Costing

| Component | Include |
|-----------|---------|
| **Direct costs** | Infrastructure, licenses, cloud consumption, support contracts |
| **Labor costs** | Staff time allocated to the service (development, operations, support) |
| **Shared costs** | Network, security, management tools, facilities (allocated proportionally) |
| **Overhead** | PMO, governance, compliance, vendor management |
| **Depreciation** | Capitalized assets allocated over useful life |

---

## 10. Regulated & Institutional Financial Context

### Institutional Budget Cycle

```
  Budget        Estimates     Appropriation    In-Year         Year-End
  Development   Submission    (Approval)       Management      Closeout
  ───────────   ──────────    ─────────────    ────────────    ──────────
  Department    Governance    Authority to     Spend, track,   Reconcile,
  submissions   body review   spend            reallocate      lapse/carry
  (6-12 months before fiscal year)              (during year)   forward
```

### Institutional Financial Constraints

| Constraint | Implication for Projects |
|------------|------------------------|
| **Fiscal year boundaries** | Unspent funds may lapse; creates "use it or lose it" pressure at year-end |
| **Appropriation limits** | Cannot exceed approved spending authority; overruns require supplementary approval |
| **Multi-year funding** | Requires specific approval; subject to annual re-confirmation and potential reduction |
| **Transfer payments** | Funding to external entities (partners, agencies, subsidiaries) involves additional oversight and reporting |
| **Capital planning** | Capital projects require separate approval stream; thresholds vary by organization |
| **Procurement rules** | Institutional procurement directives and policies set mandatory competitive processes |

### Extended Business Case Requirements

In regulated or accountability-driven environments, business cases typically require additional elements:

| Requirement | Purpose |
|-------------|---------|
| **Strategic alignment** | Demonstrate alignment with organizational mandate, leadership priorities, and strategic plan |
| **Options analysis** | Minimum three options including status quo; documented evaluation criteria |
| **Risk assessment** | Including reputational, political, and privacy risks |
| **Privacy impact assessment** | Required for systems handling personal information (PIA/TRA) |
| **Accessibility assessment** | Compliance with applicable accessibility standards (WCAG, etc.) |
| **Community and stakeholder impact** | Duty to consult or engage affected communities where applicable |
| **FTE impact** | Workforce implications (new positions, redeployment, labour considerations) |
| **Sustainability** | Environmental and social impact considerations |
| **Exit strategy** | How to unwind or transition if the initiative is not successful |

### Institutional Cost Categories

| Category | Examples |
|----------|----------|
| **Salary and wages** | FTEs, overtime, benefits, secondments, acting assignments |
| **Professional services** | Consulting, staff augmentation, specialized expertise |
| **Technology** | Licenses, cloud services, hardware, telecommunications |
| **Transfer payments** | Grants, subsidies, contributions to partner organizations |
| **Capital** | Major IT infrastructure, facility modifications, large development projects |
| **Operating** | Travel, training, supplies, hosting, maintenance |

### Funding Models

| Model | Characteristics |
|-------|----------------|
| **Base funding** | Ongoing departmental allocation; stable but limited flexibility |
| **Project-specific** | Approved for a defined initiative; time-bound with clear deliverables |
| **Transformation fund** | Central fund for modernization; competitive application process |
| **Shared services** | Costs distributed across departments/divisions for common platforms |
| **Revenue-generating** | Services that generate fees (licensing, permits); revenue offsets costs |
| **Intergovernmental / interagency transfer** | Funding from another level of the organization or external body; comes with accountability and reporting requirements |

---

> **Note**: For foundational budget and cost management concepts, see [Project Management](project_management.md). For accounting standards, PSAS/IFRS, capital assets, audit, and internal controls, see [Enterprise Financial Standards](enterprise_finance.md). For cloud financial management and FinOps, see [FinOps](finops.md). For IT-specific project patterns, see [ITPM](itpm.md). For financial reporting formats and dashboards, see [Reporting](reporting.md). For enterprise risk including financial risk, see [Risk Management](risk_management.md).
