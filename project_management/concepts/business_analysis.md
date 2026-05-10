# Business Analysis & Process Improvement

**Scope**: Business analysis practices, requirements engineering, business process analysis and improvement, business capability assessment, current/future state analysis, and feasibility evaluation in enterprise and regulated environments.

**Purpose**: Use this as a reference for structured analysis of business problems, processes, and capabilities. For business case development and financial justification, see [Business & Financial Practices](business_financial.md). For organizational change management, see [Change Management](change_management.md). For product-oriented discovery and validation, see [Product Management](product_management.md). For project-level scope and requirements, see [Project Management](project_management.md).

## Table of Contents

- [1. Business Analysis Fundamentals](#1-business-analysis-fundamentals)
- [2. Requirements Engineering](#2-requirements-engineering)
- [3. Elicitation Techniques](#3-elicitation-techniques)
- [4. Business Process Analysis](#4-business-process-analysis)
- [5. Business Process Improvement](#5-business-process-improvement)
- [6. Business Capability Assessment](#6-business-capability-assessment)
- [7. Current State / Future State Analysis](#7-current-state--future-state-analysis)
- [8. Feasibility Analysis](#8-feasibility-analysis)
- [9. Decision Analysis](#9-decision-analysis)
- [10. Enterprise & Organizational Analysis](#10-enterprise--organizational-analysis)
- [11. BA in Regulated & Institutional Environments](#11-ba-in-regulated--institutional-environments)
- [12. BA Anti-Patterns](#12-ba-anti-patterns)

---

## 1. Business Analysis Fundamentals

### What Business Analysis Is

Business analysis is the practice of identifying business needs, defining solutions, and managing requirements to enable change that delivers value. It bridges the gap between stakeholders who have problems and teams who build solutions — ensuring the right problem is solved, not just that a solution is delivered.

### BA vs Adjacent Roles

| Role | Primary Focus | Overlap with BA |
|------|---------------|-----------------|
| **Business Analyst** | Problem definition, requirements, process analysis, solution evaluation | — |
| **Project Manager** | Delivery execution, schedule, budget, risk | Scope definition, stakeholder engagement |
| **Product Manager** | Product vision, strategy, market fit, prioritization | Discovery, requirements, user research |
| **Systems Analyst** | Technical design, system specifications, integration | Functional requirements, data analysis |
| **Process Engineer** | Process design, automation, operational efficiency | Process mapping, improvement |
| **Data Analyst** | Data interpretation, reporting, insights | Analytical techniques, metrics definition |

In practice, boundaries blur. In smaller organizations, one person may hold multiple roles. The discipline matters more than the title.

### BABOK Knowledge Areas

The IIBA's *Business Analysis Body of Knowledge* (BABOK) defines six knowledge areas:

| Knowledge Area | Focus |
|----------------|-------|
| **Business Analysis Planning & Monitoring** | How the BA work itself will be performed, tracked, and assessed |
| **Elicitation & Collaboration** | Drawing out, confirming, and communicating information from stakeholders |
| **Requirements Life Cycle Management** | Managing requirements from inception through retirement |
| **Strategy Analysis** | Understanding current state, defining future state, assessing risk, and defining the change strategy |
| **Requirements Analysis & Design Definition** | Structuring, specifying, modeling, and validating requirements and designs |
| **Solution Evaluation** | Assessing the performance of a solution against the actual business need |

### Core BA Competencies

| Competency | Application |
|------------|-------------|
| **Analytical thinking** | Decompose complex problems, identify root causes, evaluate options |
| **Systems thinking** | Understand how parts of a system interact; trace upstream and downstream effects of changes |
| **Facilitation** | Lead workshops, mediate conflicting requirements, build consensus |
| **Communication** | Translate between business and technical language; document clearly for diverse audiences |
| **Domain knowledge** | Understand the business context, industry, regulatory environment, and organizational culture |
| **Modeling** | Represent processes, data, rules, and interactions visually using standard notations |

---

## 2. Requirements Engineering

### Requirements Hierarchy

```
  Business Requirements
  ─────────────────────
  Why the organization is undertaking this initiative
  (objectives, constraints, success criteria)
       │
       ▼
  Stakeholder Requirements
  ────────────────────────
  What stakeholders need the solution to do for them
  (capabilities, conditions, expectations)
       │
       ▼
  Solution Requirements
  ─────────────────────
  ├── Functional: what the solution must do (behaviors, features, functions)
  └── Non-functional: how the solution must perform (performance, security, usability, scalability)
       │
       ▼
  Transition Requirements
  ───────────────────────
  What is needed to move from current to future state
  (data migration, training, parallel operations, cutover)
```

### Requirements Characteristics (INVEST for Agile, SMART for Traditional)

| Characteristic | Meaning |
|----------------|---------|
| **Complete** | Sufficient detail to be understood and implemented without ambiguity |
| **Consistent** | Does not contradict other requirements |
| **Testable** | Can be verified through testing, inspection, or demonstration |
| **Traceable** | Linked to business objectives upstream and design/test artifacts downstream |
| **Prioritized** | Ranked by business value, risk, or implementation dependency |
| **Feasible** | Technically and organizationally achievable within constraints |
| **Unambiguous** | One interpretation only; no room for conflicting readings |

### Requirements Documentation Formats

| Format | Best For | Characteristics |
|--------|----------|-----------------|
| **Business Requirements Document (BRD)** | Waterfall, regulated environments | Comprehensive, formal, signed-off as a baseline |
| **User stories** | Agile, iterative delivery | Lightweight; "As a [user], I want [goal], so that [value]" |
| **Use cases** | Complex interactions, system behavior | Actor-system interaction sequences; main flow, alternate flows, exceptions |
| **Acceptance criteria** | Any methodology | Specific, testable conditions that confirm a requirement is satisfied |
| **Process models** | Process-centric initiatives | Visual workflows showing activities, decisions, handoffs (BPMN, swimlane) |
| **Data dictionaries** | Data-intensive projects | Field-level definitions, data types, business rules, valid values |
| **Prototypes / wireframes** | UI-driven solutions, ambiguity reduction | Visual representations of the solution for stakeholder validation |

### Requirements Management

| Activity | Purpose |
|----------|---------|
| **Traceability** | Link each requirement to its business objective, design element, and test case |
| **Change control** | Formal process for evaluating, approving, and incorporating requirement changes |
| **Version control** | Track requirement evolution; maintain history of what changed and why |
| **Prioritization** | Continuously rank requirements to guide development sequencing |
| **Sign-off / approval** | Formal stakeholder agreement that requirements are correct, complete, and ready |
| **Conflict resolution** | Resolve contradictory requirements between stakeholders through facilitation and trade-off analysis |

---

## 3. Elicitation Techniques

### Technique Selection

| Technique | Best For | Strengths | Limitations |
|-----------|----------|-----------|-------------|
| **Interviews** | Deep understanding of individual perspectives, SME knowledge | Rich detail, builds rapport, uncovers tacit knowledge | Time-intensive, subject to individual bias |
| **Workshops / JAD sessions** | Cross-functional consensus, complex process flows | Collaborative, surfaces conflicts early, efficient for groups | Requires skilled facilitator, dominant voices can suppress input |
| **Observation (job shadowing)** | Understanding actual vs documented processes | Reveals workarounds and undocumented steps | Time-consuming, observer effect can alter behavior |
| **Document analysis** | Existing systems, regulations, legacy processes | Builds on existing documentation, identifies gaps | Documents may be outdated, incomplete, or aspirational |
| **Surveys / questionnaires** | Large populations, quantitative validation | Scalable, anonymous, statistical | Low response rates, limited depth, question bias |
| **Prototyping** | Ambiguous requirements, UI/UX validation | Visual, concrete, fast feedback | Can anchor stakeholders on implementation details prematurely |
| **Interface analysis** | System integrations, data exchanges | Identifies data flows, protocols, contracts between systems | Requires technical context |
| **Brainstorming** | Idea generation, early exploration | Creative, inclusive, generates volume | Needs structure to avoid unfocused output |
| **Focus groups** | Representative user input, perception testing | Group dynamics surface shared concerns | Groupthink risk, requires moderation |
| **Reverse engineering** | Legacy systems without documentation | Recovers undocumented logic and rules | Time-consuming, risk of misinterpretation |

### Elicitation Principles

- **Prepare before eliciting.** Define objectives, identify the right participants, prepare questions or a session plan. Unstructured elicitation wastes stakeholder time and produces shallow results.
- **Triangulate sources.** No single technique or stakeholder reveals the full picture. Combine interviews with observation, documents with workshops.
- **Separate stated needs from actual needs.** Stakeholders describe what they think they want. Observation and analysis reveal what they actually need.
- **Document during and after.** Capture notes in the session; formalize into structured requirements within 48 hours while context is fresh.
- **Confirm and validate.** Every elicitation output should be reviewed with stakeholders for accuracy before it becomes a requirement.

### Elicitation Anti-Patterns

| Anti-Pattern | Problem |
|-------------|---------|
| Eliciting from sponsors only | Misses the perspective of people who do the work; requirements reflect management's view, not operational reality |
| Treating elicitation as a one-time event | Requirements emerge over time; single-pass elicitation misses evolving understanding |
| Leading questions | "Don't you think we should..." biases responses and produces confirmation, not information |
| Skipping observation | Accepting documented processes at face value misses workarounds, informal processes, and actual pain points |
| No validation loop | Requirements derived from elicitation but never confirmed with stakeholders accumulate errors |

---

## 4. Business Process Analysis

### Process Hierarchy

```
  Value Chain
  ───────────
  End-to-end business activity that delivers value to a customer or stakeholder
       │
       ├── Process (Level 1)
       │     End-to-end workflow (e.g., Order-to-Cash, Hire-to-Retire)
       │          │
       │          ├── Sub-process (Level 2)
       │          │     Major phase within a process (e.g., Invoice Processing)
       │          │          │
       │          │          ├── Activity (Level 3)
       │          │          │     Discrete work step (e.g., Validate Invoice)
       │          │          │          │
       │          │          │          └── Task (Level 4)
       │          │          │                Atomic action (e.g., Check PO Match)
```

### Process Mapping Notations

| Notation | Use Case | Characteristics |
|----------|----------|-----------------|
| **BPMN 2.0** (Business Process Model and Notation) | Formal process modeling, automation, executable processes | Industry standard; pools, lanes, events, gateways, flows; supports technical precision |
| **Swimlane diagrams** | Role-based process flows, handoff analysis | Shows which actor performs which step; highlights cross-functional handoffs |
| **Value stream maps** | End-to-end flow including wait times and waste | Lean-originated; shows process time vs lead time; surfaces non-value-adding steps |
| **Flowcharts** | Simple process documentation, decision logic | Widely understood; start/end, process, decision symbols; limited precision for complex flows |
| **SIPOC** (Supplier, Input, Process, Output, Customer) | High-level process scoping | Quick overview before detailed mapping; defines process boundaries |
| **Data flow diagrams (DFD)** | Information flow analysis, system context | Shows how data moves through a system; external entities, processes, data stores |

### SIPOC Example

```
  Supplier      Input           Process              Output          Customer
  ────────      ─────           ───────              ──────          ────────
  Customer  ──▶ Purchase    ──▶ Order Processing ──▶ Confirmed   ──▶ Customer
                order                                order
  Warehouse ──▶ Inventory   ──▶ Fulfillment      ──▶ Shipped     ──▶ Customer
                data                                 product
  Finance   ──▶ Payment     ──▶ Payment           ──▶ Receipt /   ──▶ Customer /
                authorization    Processing           reconciliation   Finance
```

### Process Analysis Dimensions

| Dimension | What to Assess |
|-----------|---------------|
| **Flow** | Sequence of activities, decision points, parallel paths, loops |
| **Time** | Cycle time (active work), lead time (end-to-end including waits), wait time between steps |
| **Handoffs** | Transfers between people, teams, or systems; each handoff is a risk point for delay, error, or information loss |
| **Exceptions** | How deviations from the standard flow are handled; volume and cost of exception processing |
| **Controls** | Approval steps, validation checks, segregation of duties, audit points |
| **Automation** | Which steps are manual, semi-automated, or fully automated; automation candidates |
| **Data** | Information created, consumed, or transformed at each step; data quality dependencies |
| **Volume** | Transaction throughput; peak vs average load; scalability constraints |
| **Pain points** | Bottlenecks, rework loops, manual workarounds, complaints, known failures |

### Process Metrics

| Metric | Formula / Definition | Purpose |
|--------|---------------------|---------|
| **Cycle time** | Time from work start to work completion (active processing) | Measure process efficiency |
| **Lead time** | Time from request to delivery (includes wait time) | Measure customer experience |
| **Throughput** | Units processed per time period | Measure process capacity |
| **Error / rework rate** | % of outputs requiring correction | Measure process quality |
| **First-pass yield** | % of outputs correct on first attempt | Measure process effectiveness |
| **Process cost** | Total cost to execute one instance of the process | Measure process economics |
| **Touch points** | Number of distinct people or systems involved | Proxy for handoff risk and complexity |
| **Automation rate** | % of steps or effort that is automated | Measure automation maturity |

---

## 5. Business Process Improvement

### Improvement Approaches

| Approach | Origin | Focus | Scale |
|----------|--------|-------|-------|
| **Lean** | Toyota Production System | Eliminate waste, maximize value-adding work | Continuous, incremental |
| **Six Sigma** | Motorola / GE | Reduce variation and defects using statistical methods | Structured projects (DMAIC) |
| **Lean Six Sigma** | Combined | Waste elimination + variation reduction | Hybrid |
| **BPR** (Business Process Reengineering) | Hammer & Champy | Radical redesign of end-to-end processes | Transformational |
| **Kaizen** | Japanese manufacturing | Small, continuous improvements by frontline workers | Incremental, team-driven |
| **Theory of Constraints (TOC)** | Goldratt | Identify and exploit the system's bottleneck | Focused improvement at constraint |

### Lean: Eight Wastes (DOWNTIME)

| Waste | Definition | Business Process Example |
|-------|-----------|--------------------------|
| **D**efects | Errors requiring rework or correction | Incorrect data entry requiring manual fix |
| **O**verproduction | Producing more than needed or before needed | Generating reports no one reads |
| **W**aiting | Idle time between process steps | Application pending approval for days |
| **N**on-utilized talent | Underusing people's skills and knowledge | Senior analysts doing manual data entry |
| **T**ransportation | Unnecessary movement of materials or information | Emailing spreadsheets between departments for manual re-entry |
| **I**nventory | Excess work-in-progress or unprocessed backlog | Hundreds of unprocessed requests in a queue |
| **M**otion | Unnecessary steps or actions within a task | Switching between five systems to complete one transaction |
| **E**xtra processing | Doing more work than the customer requires | Multiple approval layers for low-risk decisions |

### Six Sigma: DMAIC

```
  Define ──▶ Measure ──▶ Analyze ──▶ Improve ──▶ Control
     │           │           │           │           │
  Problem     Baseline    Root cause   Solution    Sustain
  scope       data        analysis     design      gains
  CTQ         process     statistical  pilot       SPC
  metrics     capability  methods      implement   monitoring
```

| Phase | Key Activities | Key Outputs |
|-------|----------------|-------------|
| **Define** | Define the problem, scope, goals, and stakeholders; identify CTQ (Critical to Quality) characteristics | Project charter, SIPOC, voice of customer |
| **Measure** | Collect baseline data on current process performance; validate measurement system | Process map (as-is), baseline metrics, measurement plan |
| **Analyze** | Identify root causes of defects or variation; test hypotheses with data | Root cause analysis, Pareto charts, fishbone diagrams, validated causes |
| **Improve** | Develop, pilot, and implement solutions that address root causes | Solution design, pilot results, implementation plan |
| **Control** | Sustain improvements; monitor performance; standardize | Control plan, updated SOPs, statistical process control charts |

### Improvement Prioritization

| Criterion | Question |
|-----------|----------|
| **Impact** | How significantly will this improvement affect cycle time, cost, quality, or customer experience? |
| **Effort** | What resources, time, and organizational energy are required? |
| **Risk** | What could go wrong? What are the dependencies? |
| **Readiness** | Is the organization ready for this change? Are sponsors and resources available? |
| **Quick win potential** | Can this be implemented quickly to build momentum and credibility? |
| **Strategic alignment** | Does this improvement support broader organizational goals? |

### Process Improvement Anti-Patterns

| Anti-Pattern | Problem |
|-------------|---------|
| Automating a bad process | Technology amplifies waste; fix the process before automating it |
| Improving in isolation | Optimizing one step while creating bottlenecks upstream or downstream |
| No baseline measurement | Cannot demonstrate improvement without measuring the starting state |
| Solution before analysis | Jumping to a fix without understanding root causes; solves the wrong problem |
| Ignoring the people side | Process changes that don't account for change management, training, and adoption |
| One-time event | Improvement treated as a project rather than a continuous discipline |

---

## 6. Business Capability Assessment

### What a Business Capability Is

A business capability describes *what* an organization does — independent of how, where, or by whom. Capabilities are stable over time even as processes, technology, and organizational structures change. They provide a strategy-neutral vocabulary for analyzing the business.

### Capability vs Process vs Function

| Concept | Definition | Example | Stability |
|---------|-----------|---------|-----------|
| **Capability** | What the organization does (ability to achieve an outcome) | "Customer Onboarding" | High — changes rarely |
| **Process** | How the capability is executed (sequence of steps) | "New customer registration workflow" | Medium — changes with improvement |
| **Function / Department** | Who performs the work (organizational unit) | "Customer Success Team" | Low — changes with re-orgs |
| **Technology** | What systems support the capability | "CRM platform" | Low — changes with technology cycles |

### Capability Map Structure

```
  Enterprise Capability Map
  ─────────────────────────
  ┌────────────────────────────────────────────────────────────────┐
  │  Strategic Capabilities                                        │
  │  (Direction-setting: Strategy, Portfolio Mgmt, Innovation)     │
  ├────────────────────────────────────────────────────────────────┤
  │  Core Capabilities                                             │
  │  (Value-creating: Product Development, Service Delivery,       │
  │   Customer Management, Order Fulfillment)                      │
  ├────────────────────────────────────────────────────────────────┤
  │  Supporting Capabilities                                       │
  │  (Enabling: HR, Finance, IT, Legal, Procurement, Facilities)   │
  └────────────────────────────────────────────────────────────────┘
```

### Capability Assessment Dimensions

| Dimension | What to Assess | Assessment Scale |
|-----------|---------------|-----------------|
| **Maturity** | How well-developed is this capability? | Ad hoc → Repeatable → Defined → Managed → Optimizing |
| **Performance** | How well does this capability deliver outcomes? | Below expectations → Meets → Exceeds |
| **Strategic importance** | How critical is this capability to strategy execution? | Low → Medium → High → Differentiating |
| **Current investment** | How much is the organization spending on this capability? | Under-invested → Appropriate → Over-invested |
| **Technology enablement** | How well do systems and tools support this capability? | Manual → Partially automated → Fully enabled |
| **Talent** | Does the organization have the right people and skills? | Critical gaps → Some gaps → Adequate → Strong |
| **Process maturity** | Are processes supporting this capability standardized and effective? | Undefined → Inconsistent → Standardized → Optimized |

### Capability Heat Map

```
                       Strategic Importance
                     Low      Medium      High
                   ┌────────┬──────────┬──────────┐
  Maturity  High   │ Reduce │ Maintain │ Leverage │
                   │ invest │          │          │
                   ├────────┼──────────┼──────────┤
           Medium  │ Monitor│ Develop  │ Invest   │
                   │        │          │          │
                   ├────────┼──────────┼──────────┤
           Low     │ Accept │ Plan     │ CRITICAL │
                   │ risk   │ improve  │ GAP      │
                   └────────┴──────────┴──────────┘
```

High strategic importance + low maturity = critical gap requiring investment. Low strategic importance + high maturity = potential over-investment.

### Capability Assessment Process

| Step | Activity | Output |
|------|----------|--------|
| 1 | **Define scope** | Identify which capabilities to assess (full enterprise or targeted domain) |
| 2 | **Build capability map** | Hierarchical map of capabilities at 2-3 levels of decomposition |
| 3 | **Assess current state** | Rate each capability across assessment dimensions using workshops, interviews, and data |
| 4 | **Determine strategic importance** | Align capabilities to strategic objectives; identify differentiators |
| 5 | **Identify gaps** | Overlay current maturity against strategic importance; highlight critical gaps |
| 6 | **Prioritize investments** | Rank capability gaps by strategic impact, feasibility, and urgency |
| 7 | **Define improvement roadmap** | Sequence investments into initiatives with timelines, owners, and targets |

### Capability Assessment Pitfalls

| Pitfall | Problem |
|---------|---------|
| Confusing capabilities with departments | Capabilities cross organizational boundaries; mapping to org chart creates blind spots |
| Too granular too early | Starting with Level 4 detail before establishing Level 1-2 agreement wastes effort and creates noise |
| Assessment without action | Producing a heat map that sits on a shelf; assessment must connect to investment decisions |
| Self-assessment bias | Teams rating their own capabilities tend toward optimism; use external validation or evidence-based rubrics |
| Static snapshot | Capability needs evolve with strategy; assessments must be refreshed periodically (annually minimum) |

---

## 7. Current State / Future State Analysis

### Analysis Framework

```
  Current State           Gap Analysis           Future State
  (As-Is)                                        (To-Be)
  ─────────────           ────────────           ────────────
  How things work      What must change        How things should work
  today                to get there            in the target state
       │                     │                      │
  Processes              People gaps             Redesigned processes
  Technology             Process gaps            New/modified technology
  People & skills        Technology gaps         Required skills & roles
  Pain points            Organizational gaps     Resolved pain points
  Performance data       Risk & constraints      Target performance
```

### Current State Analysis

| Activity | Purpose | Techniques |
|----------|---------|-----------|
| **Process mapping** | Document how work actually flows (not how it's supposed to) | Observation, interviews, swimlane diagrams, value stream maps |
| **Stakeholder analysis** | Identify who is involved, affected, or influential | Stakeholder interviews, RACI, power/interest grid |
| **Technology inventory** | Catalog systems, integrations, data flows | System inventory, architecture diagrams, interface analysis |
| **Performance baselining** | Measure current metrics (time, cost, quality, volume) | Data analysis, process mining, operational reports |
| **Pain point identification** | Surface problems, workarounds, complaints, risks | Interviews, workshops, observation, support ticket analysis |
| **Constraint mapping** | Identify regulatory, contractual, cultural, and technical constraints | Document analysis, compliance review, stakeholder input |

### Gap Analysis

| Gap Category | Assessment Questions |
|-------------|---------------------|
| **Process gaps** | Which processes are missing, broken, manual, redundant, or misaligned with the future state? |
| **Technology gaps** | Which systems need to be introduced, replaced, integrated, or retired? |
| **People / skill gaps** | What new skills, roles, or capacity are needed? What retraining is required? |
| **Data gaps** | What data is missing, duplicated, inconsistent, or in the wrong system? |
| **Policy / governance gaps** | Which policies, standards, or controls need to be created, updated, or retired? |
| **Organizational gaps** | Are current reporting structures, decision rights, and incentives aligned with the future state? |

### Future State Design Principles

- **Start from the business outcome, not the current process.** Redesigning around existing steps risks carrying forward inherited waste.
- **Design for the constraint, not the average.** Future state processes must handle peak load, exceptions, and edge cases, not just the happy path.
- **Account for transition.** The gap between current and future state includes transition requirements — data migration, parallel operations, phased rollouts, training.
- **Validate with stakeholders who do the work.** Future state designed in a conference room without frontline input will have adoption problems.
- **Define measurable targets.** The future state must have specific performance targets that can be compared to the current state baseline.

---

## 8. Feasibility Analysis

### Feasibility Dimensions

| Dimension | Assessment Question | Key Considerations |
|-----------|--------------------|--------------------|
| **Technical** | Can we build it with available or obtainable technology? | Platform maturity, integration complexity, data availability, performance requirements, security constraints |
| **Operational** | Can the organization operate and sustain the solution? | Staffing, skills, process changes, support model, operational readiness |
| **Economic** | Does the investment make financial sense? | Cost-benefit analysis, ROI, payback period, NPV, funding availability |
| **Schedule** | Can we deliver within the required timeframe? | Complexity, dependencies, resource availability, regulatory timelines |
| **Organizational** | Is the organization ready and willing to adopt this change? | Sponsorship, culture, change capacity, political dynamics |
| **Legal / regulatory** | Does the solution comply with applicable laws, regulations, and policies? | Privacy, accessibility, procurement rules, contractual obligations, industry regulations |

### Feasibility Assessment Output

| Component | Content |
|-----------|---------|
| **Recommendation** | Feasible, feasible with conditions, or not feasible — with clear rationale |
| **Assumptions** | Conditions the assessment depends on |
| **Constraints** | Limitations that bound the solution |
| **Risks** | Risks identified during assessment with preliminary severity |
| **Dependencies** | External factors that must be in place for success |
| **Alternative approaches** | If the primary approach is not feasible, what alternatives exist? |

### Feasibility vs Business Case

| | Feasibility Analysis | Business Case |
|---|---------------------|---------------|
| **Question** | Can we do this? | Should we do this? |
| **Timing** | Early — before significant investment in planning | After feasibility confirms viability |
| **Depth** | Preliminary assessment across key dimensions | Detailed analysis with cost-benefit, options, and implementation plan |
| **Output** | Go / no-go / conditional recommendation | Investment decision with recommended option |

---

## 9. Decision Analysis

### Decision Analysis Techniques

| Technique | When to Use | Approach |
|-----------|-------------|----------|
| **Weighted scoring** | Comparing multiple options against defined criteria | Define criteria, assign weights, score each option, calculate weighted totals |
| **Decision matrix** | Structured comparison with trade-offs | Criteria in rows, options in columns, rate and compare |
| **Cost-benefit analysis** | Financial comparison of alternatives | Quantify costs and benefits for each option over a defined horizon |
| **Force field analysis** | Understanding drivers and barriers | List forces for and against a change; assess strength of each |
| **Decision tree** | Sequential decisions with probabilistic outcomes | Map decision points, chance events, and outcomes; calculate expected values |
| **Multi-criteria decision analysis (MCDA)** | Complex decisions with diverse stakeholder values | Structured evaluation across quantitative and qualitative criteria with stakeholder weighting |
| **Pugh matrix** | Early-stage concept selection | Score alternatives against a baseline (better/same/worse) |

### Structured Decision Process

```
  Frame ──▶ Gather ──▶ Analyze ──▶ Decide ──▶ Communicate ──▶ Review
    │          │          │           │            │               │
  Define     Elicit     Evaluate    Select       Explain         Assess
  the        options    trade-offs  option       rationale       outcome
  question   & criteria             & document   & decision      vs expected
```

### Decision Documentation

| Element | Purpose |
|---------|---------|
| **Decision statement** | What was decided |
| **Context** | Why this decision was needed; what triggered it |
| **Options considered** | All alternatives evaluated, including rejected options |
| **Evaluation criteria** | How options were compared (with weights if applicable) |
| **Rationale** | Why the selected option was chosen; trade-offs accepted |
| **Assumptions** | Conditions the decision depends on |
| **Risks** | Risks associated with the decision |
| **Owner** | Who made the decision and who is accountable for execution |
| **Review date** | When the decision should be revisited (if applicable) |

### Decision Anti-Patterns

| Anti-Pattern | Problem |
|-------------|---------|
| Analysis paralysis | Delaying decisions indefinitely in pursuit of perfect information |
| HiPPO decisions | Highest-paid person's opinion overrides structured analysis |
| Sunk cost bias | Continuing with a failing approach because of past investment |
| Undocumented decisions | Decisions made verbally with no record; rationale lost, revisited repeatedly |
| False consensus | Assuming silence means agreement; dissent suppressed or not sought |
| Anchoring on first option | First option presented dominates evaluation; alternatives not seriously considered |

---

## 10. Enterprise & Organizational Analysis

### Organizational Assessment Dimensions

| Dimension | Assessment Focus |
|-----------|-----------------|
| **Strategy alignment** | Are initiatives, processes, and capabilities aligned with strategic objectives? |
| **Operating model** | How is work organized, governed, and delivered across the enterprise? (centralized, federated, hybrid) |
| **Organizational structure** | Reporting lines, decision rights, spans of control, matrix relationships |
| **Culture** | Norms, values, risk tolerance, collaboration patterns, innovation climate |
| **Governance** | Decision-making bodies, approval processes, accountability structures |
| **Capacity** | Organizational ability to absorb change, execute initiatives, and sustain operations simultaneously |

### Stakeholder Analysis for BA

| Technique | Purpose | Output |
|-----------|---------|--------|
| **RACI matrix** | Clarify roles for each deliverable or decision | Responsibility assignments: Responsible, Accountable, Consulted, Informed |
| **Power / interest grid** | Prioritize stakeholder engagement | Engagement strategy per quadrant |
| **Onion diagram** | Map stakeholder proximity to the solution | Layers: direct users → indirect users → affected parties → external |
| **Stakeholder impact map** | Assess how each group is affected by the change | Impact severity, readiness, required support |

### Enterprise Analysis Outputs

| Output | Purpose |
|--------|---------|
| **Business architecture** | Capability map, value streams, organizational structure, strategic alignment |
| **Process architecture** | Enterprise process hierarchy (Level 0-3), process ownership, cross-functional flows |
| **Information architecture** | Key data entities, ownership, flows between systems, master data identification |
| **Technology landscape** | Application portfolio, integration map, technology lifecycle status |
| **Gap analysis** | Delta between current state and strategic target across all dimensions |
| **Investment roadmap** | Prioritized sequence of initiatives to close gaps, with timelines and dependencies |

---

## 11. BA in Regulated & Institutional Environments

### Additional BA Considerations

| Consideration | Implication |
|---------------|-------------|
| **Audit trail** | Requirements decisions, changes, and approvals must be documented and traceable |
| **Privacy impact** | Solutions handling personal data require privacy impact assessments (PIA/TRA) as a requirements input |
| **Accessibility** | Requirements must include accessibility standards compliance (WCAG 2.1 AA minimum) |
| **Bilingual / multilingual** | Requirements may need to address content, interfaces, and communications in multiple languages |
| **Procurement constraints** | BA must understand procurement timelines, sole-source thresholds, and vendor evaluation obligations |
| **Policy compliance** | Requirements must align with applicable organizational directives, mandates, and standards |
| **Consultation obligations** | Duty to consult affected communities, labour representatives, or partner organizations may affect requirements scope and timeline |

### Requirements in Regulated Contexts

| Practice | Purpose |
|----------|---------|
| **Requirements traceability matrix (RTM)** | Link every requirement to its source (regulation, policy, stakeholder) and its downstream artifacts (design, test case) |
| **Formal sign-off** | Business owner, privacy officer, accessibility lead, and technical authority approve requirements baseline |
| **Change impact assessment** | Every requirement change assessed for impact on compliance, privacy, accessibility, and security |
| **Assumption logging** | All assumptions documented, assigned owners, and reviewed at phase gates |
| **Decision log** | Every significant BA decision recorded with rationale, alternatives considered, and approver |

---

## 12. BA Anti-Patterns

### Common BA Failures

| Anti-Pattern | Problem | Countermeasure |
|-------------|---------|----------------|
| **Solution-first thinking** | Starting with "we need [tool/platform]" before understanding the problem | Enforce problem statement before solution discussion; ask "what business outcome?" |
| **Requirements as wish lists** | Stakeholders submit feature requests without business justification | Require business value statement and priority rationale for every requirement |
| **Gold plating** | Adding features or complexity beyond what stakeholders need | Trace every requirement to a business objective; cut what can't be linked |
| **Scope defined by loudest voice** | Influential stakeholders dominate requirements; quieter groups are underserved | Structured elicitation across all affected groups; workshop facilitation |
| **Copy-paste requirements** | Reusing requirements from a past project without validating fit | Review every requirement for current-project relevance and context |
| **Confusing "as-is" with "to-be"** | Documenting current processes and calling them requirements | Explicitly separate current state analysis from future state design |
| **Requirements sign-off without understanding** | Stakeholders sign off to avoid delay, not because they've reviewed | Walk through requirements with stakeholders; use prototypes to validate understanding |
| **Overspecification** | Prescribing implementation details in business requirements | Keep business requirements at the "what and why" level; leave "how" to solution design |
| **Ignoring non-functional requirements** | Focusing only on features; neglecting performance, security, scalability, accessibility | Use a non-functional requirements checklist at every requirements review |
| **No BA involvement post-implementation** | BA disengages after requirements are delivered; no solution evaluation | BA participates in solution evaluation to assess whether the business need was actually met |

---

> **Note**: For business case development, investment appraisal, and cost estimation, see [Business & Financial Practices](business_financial.md). For organizational change management, see [Change Management](change_management.md). For product-oriented discovery, user research, and prioritization, see [Product Management](product_management.md). For project-level scope and requirements management, see [Project Management](project_management.md). For enterprise risk management, see [Risk Management](risk_management.md).
