# Project Management — Systems Thinking Reference

Project management is the practice of delivering outcomes through people, under constraints, with incomplete information. Every concept in this directory—scope management, stakeholder engagement, risk registers, Agile ceremonies, roadmaps, reporting—is a tool for navigating one part of that challenge. They all connect through one lifecycle and four patterns unique to how projects behave.

```
  ┌──────────────────────────────────────────────────────────────────┐
  │                  THE PROJECT LIFECYCLE                           │
  │                                                                  │
  │ Discover ──▶ Define ──▶ Plan ──▶ Deliver ──▶ Control ──▶ Adapt│
  │       ▲                                                     │    │
  │       └─────────────── lessons learned ────────────────────┘     │
  └──────────────────────────────────────────────────────────────────┘
```

| Phase | What happens | Key docs |
|-------|-------------|----------|
| **Discover** | Understand the real problem, stakeholder landscape, history, constraints, success criteria | [Critical Thinking](critical_thinking.md), [Product Management](../concepts/product_management.md) |
| **Define** | Charter, scope statement, methodology selection, feasibility, team structure | [Project Management](../concepts/project_management.md), [Methodologies](../concepts/methodologies.md) |
| **Plan** | WBS, schedule, budget, risk register, communication plan, roadmap | [Roadmap](../concepts/roadmap.md), [Project Management](../concepts/project_management.md) |
| **Deliver** | Execute work, manage team, coordinate vendors, communicate status | [ITPM](../concepts/itpm.md), [SDLC](../concepts/sdlc.md) |
| **Control** | Track progress, manage changes, manage risks, report status, validate scope | [Reporting](../concepts/reporting.md), [Project Management](../concepts/project_management.md) |
| **Adapt** | Lessons learned, retrospectives, process improvement, knowledge transfer | [Critical Thinking](critical_thinking.md), [Methodologies](../concepts/methodologies.md) |

Governance is not a phase—it runs across all others: stakeholder management, change control, compliance, escalation paths, quality gates. If governance only appears as a gate before go-live, it catches problems at maximum cost.

In Agile contexts, Define-Plan-Deliver-Control compress into sprint-level cycles. The lifecycle still applies—Discover still happens, Adapt still happens—but at a faster cadence with smaller scope per iteration.

---

## Governing Principles

These 10 rules explain 80% of correct project management decisions. When in doubt, check here first.

1. **Understand before acting.** Jumping to solutions before understanding the problem leads to rework, scope disputes, and stakeholder misalignment. It feels fast but costs more. The stated objectives and the actual success criteria are often different—a project can meet every requirement and still be considered a failure.
2. **Constraints define the solution space, not preferences.** Budget, timeline, regulatory requirements, team capacity, political dynamics, organizational culture—these determine what's possible. Different constraints yield different correct answers. If someone proposes the same approach regardless of constraints, they're applying a template, not solving the problem.
3. **Stakeholder alignment degrades continuously.** New stakeholders appear, priorities shift, political dynamics change, people forget what was agreed. Small misunderstandings compound into large conflicts. Alignment is ongoing work, not a one-time meeting. → *Stakeholder Drift Pattern*
4. **Scope must be actively defended.** Without explicit change control, projects absorb adjacent problems until they collapse under their own weight. Scope creep is the default trajectory—every stakeholder has "one more thing." → *Scope Absorption Pattern*
5. **Outcomes over outputs.** "Shipped 14 features" is output. "Reduced processing time by 40%" is outcome. Measure what matters to stakeholders, not what's easy to count. Product thinking (solve problems) beats feature thinking (build things).
6. **Start with the context, not the methodology.** "We should use Agile" is not a decision. Evolving requirements + available stakeholders + experienced team → iterative. Stable requirements + regulatory compliance + predictable scope → predictive. Match the approach to the uncertainty, not the trend.
7. **Governance should be proportional to risk.** Not every project needs a steering committee, a change advisory board, and weekly status reports. Over-governance slows delivery without reducing risk. Under-governance loses control. Scale governance to the project's actual risk profile. → *Governance Accumulation Pattern*
8. **Bad news early is information; bad news late is a surprise.** Surprises destroy trust. "We'll miss the March date by 3 weeks — here's our recovery plan" is professional. Discovering the same thing in March is a career-limiting event. → *Trust-Velocity Pattern*
9. **Trust is built incrementally and broken instantly.** Trust comes from demonstrated reliability on small commitments, delivered consistently. Once broken, it takes an order of magnitude more effort to rebuild. Commit to small, visible deliverables and hit them.
10. **Every process has a cost.** Status meetings, approval gates, documentation requirements all consume team capacity. Justify each one against the risk it actually mitigates. If a gate only adds delay without reducing risk, remove or streamline it. → *Governance Accumulation Pattern*

---

## Repeating Patterns

Four structures unique to how projects behave recur across every topic.

### 1. The Scope Absorption Pattern

Projects expand to fill available attention. Without active boundary defense, a focused project becomes a program, then a transformation initiative, then an organizational priority that nobody can define.

```
  "While we're at it..."    "Can we also..."    "This really should include..."
         │                       │                        │
         ▼                       ▼                        ▼
  Focused scope ──▶ expanded scope ──▶ unclear scope ──▶ undeliverable scope
       (clear)        (growing)          (fuzzy)            (collapse)
```

**Instances**:
- Stakeholder adds "just one more requirement" repeatedly — each one small, collectively fatal
- Adjacent organizational problem gets absorbed because "the project team is already working in that area"
- Success criteria expand post-approval — "we also need it to do X" without adjusting timeline or budget
- Technical scope absorbs infrastructure modernization alongside the business deliverable

**Countermeasure**: Formal change control. Every scope addition gets impact-assessed against the triple constraint. The question is not "is this a good idea?" but "what does it cost, and what do we give up to include it?"

### 2. The Governance Accumulation Pattern

Every failure adds a process. After each incident, the organization adds a gate, a review, an approval, a report. Nobody removes the old ones. Governance accumulates until it costs more than the work it governs.

```
  Incident occurs
    → new approval gate added
      → another incident → another review layer
        → another audit finding → another report required
          → governance overhead exceeds productive work
            → teams work around governance → shadow processes
              → governance loses visibility → more governance added
```

**Instances**:
- Change advisory board that reviews every change, regardless of risk level
- Weekly status reports that nobody reads, but nobody cancels
- Multi-layer approvals that add days to decisions without improving decision quality
- Documentation requirements created for auditors that nobody else uses

**Countermeasure**: Audit governance periodically. For each gate, ask: does this actually reduce risk? If it only adds delay, remove or streamline it. Design governance for flow, not control. Track cycle time to make overhead visible.

### 3. The Stakeholder Drift Pattern

Alignment is not static. Stakeholders change roles, new ones appear, political priorities shift, contexts evolve, people forget what was agreed. Misalignment compounds silently until it surfaces as conflict, usually at the worst possible moment.

```
  Initial alignment (kickoff)
    → time passes, context changes
      → stakeholder priorities shift quietly
        → PM assumes old alignment still holds
          → deliverable doesn't match current expectations
            → "this isn't what I asked for"
              → rework, conflict, trust erosion
```

**Instances**:
- Executive sponsor changes — new sponsor has different priorities, but nobody re-validates scope
- Election cycle shifts political priorities — project objectives no longer align with new mandate
- Requirements agreed in Q1 no longer reflect business needs by Q3 — but nobody formally revisits them
- Key stakeholder goes quiet — interpreted as agreement, actually disengagement

**Countermeasure**: Continuous re-alignment, not one kickoff meeting. Regular stakeholder check-ins. Re-validate assumptions at phase transitions. Treat silence as a risk signal, not agreement.

### 4. The Trust-Velocity Feedback Loop

Trust determines autonomy. Autonomy determines speed. Speed determines delivery. Delivery determines trust. This loop runs in both directions.

```
  Positive loop:
    Small wins ──▶ trust ──▶ autonomy ──▶ fast decisions ──▶ more delivery
         ▲                                                        │
         └────────────────────────────────────────────────────────┘

  Negative loop:
    Missed deadline ──▶ surprise ──▶ more oversight ──▶ slower delivery
         ▲                                                     │
         └─────────────────── more misses ─────────────────────┘
```

**Positive loop**: Project delivers small visible results early → stakeholders trust the team → governance lightens → team moves faster → more delivery → more trust.

**Negative loop**: Project misses a commitment → stakeholders lose confidence → add oversight, reporting, checkpoints → team spends more time reporting and less time delivering → more misses → more oversight.

**Break a negative loop** by resetting, not accelerating. Re-baseline honestly. Commit to a small, achievable deliverable. Hit it. Then commit to the next one. Trust is rebuilt through demonstrated reliability, not promises.

---

## Never Rules & First-Step Rules

### Never Rules

| # | Rule | Because |
|---|------|---------|
| 1 | Never skip stakeholder identification | Surprises from unknown stakeholders are the most expensive kind |
| 2 | Never present problems without proposed options | Transfers full burden to the recipient; signals no analysis done |
| 3 | Never commit scope, time, AND cost without identifying the flex variable | The triple constraint always has a variable — hiding it doesn't eliminate it |
| 4 | Never add people to a late project without decomposing the work first | Brooks's Law: communication paths grow as n(n-1)/2; more people ≠ more speed |
| 5 | Never use "everything is on track" as a status | Define specific criteria for "on track." Vague status hides problems until they're crises |
| 6 | Never agree to scope without a change control process | Scope without change control = scope creep by default → *Scope Absorption Pattern* |
| 7 | Never skip the postmortem | The same failure will recur. Blame prevents learning; blameless analysis prevents recurrence |
| 8 | Never conflate activity with progress | "Completed 14 story points" is activity. "Authentication module ready for UAT" is progress |
| 9 | Never sit on bad news | Bad news delivered early is information. Bad news delivered late is a surprise that destroys trust → *Principle 8* |
| 10 | Never treat a recurring failure as a one-off event | Recurrence means systemic cause. Fix the system, not the symptom |

### First-Step Rules

| Situation | First step | Why |
|-----------|-----------|-----|
| Inheriting a project | Read the charter, talk to the team, validate assumptions | *Principle 1*: understand before acting |
| "The project is behind schedule" | Ask: behind on what? Check scope, estimates, resources, dependencies | "Behind schedule" is a symptom, not a diagnosis |
| Stakeholder conflict | Clarify what each side actually needs (not positions, interests) | Most conflicts are misunderstandings, not genuine disagreements |
| New methodology request | List constraints: requirements stability, team experience, regulatory, size | *Principle 6*: context determines methodology |
| Scope change request | Impact-assess against triple constraint before saying yes or no | Every scope change has a cost — make it visible |
| Project recovery needed | Stop and reassess before accelerating. Re-baseline honestly | Adding pressure to a troubled project makes it worse |
| Executive asks for status | Lead with impact and decisions needed, not activity | *Principle 8*: executives act on conclusions, not methodology |
| Team morale is low | Talk to individuals privately. Ask what's frustrating, not what's wrong | The team closest to the work knows what's going wrong |
| Vendor not delivering | Check the contract (SOW, SLAs, acceptance criteria). Then talk | Know your leverage before the conversation |
| Everything seems fine | Look for the *Governance Accumulation Pattern* and *Stakeholder Drift* | "Fine" is when problems compound invisibly |

---

## Boundary Distinctions

Confusable concepts with the single property that separates them.

| Pair | Distinguishing property | Decision rule |
|------|------------------------|---------------|
| **Project vs operations** | Temporality | Unique outcome with a defined end → project. Ongoing, repetitive → operations |
| **Product manager vs project manager** | What vs how | What to build and why (strategy, ongoing) → product manager. How and when to deliver (execution, temporary) → project manager |
| **Risk vs issue** | Time orientation | Uncertain future event → risk (manage with probability × impact). Current problem → issue (resolve now) |
| **Roadmap vs project plan** | Level of detail | Strategic overview for stakeholders → roadmap. Detailed tactical execution for the team → project plan |
| **Output vs outcome** | What is measured | Work produced (features shipped, tasks completed) → output. Business impact achieved (revenue, efficiency, satisfaction) → outcome |
| **Quality vs grade** | Flexibility | Degree deliverables meet requirements → quality (must meet). Category or rank → grade (can vary by design) |
| **Program vs portfolio** | Relationship | Related projects with shared objectives → program. Collection of projects for strategic alignment → portfolio |
| **Scope creep vs change management** | Control | Uncontrolled expansion without impact assessment → scope creep. Controlled change with impact assessment and approval → change management |
| **Waterfall vs Agile** | When scope is locked | Scope locked early, sequential phases → Waterfall. Scope evolves, iterative delivery → Agile. Most real projects are hybrid |
| **Essential vs accidental complexity** | Source | Inherent in the problem (regulatory, stakeholder diversity) → essential. Introduced by your approach (over-governance, tool proliferation) → accidental. Minimize accidental |
| **Velocity vs throughput** | Unit | Story points completed per sprint → velocity (team planning). Work items completed per time period → throughput (flow metric) |
| **Sprint review vs retrospective** | Subject | What was built → sprint review (product, stakeholder feedback). How we worked → retrospective (process, team improvement) |

---

## Decision Engine: Methodology Selection

```
  1. What are the requirements like?
     Stable, well-understood        → predictive approach viable
     Evolving, unclear, discovered  → iterative approach required
     → If requirements are evolving AND stakeholders aren't available
       for frequent feedback, you have a deeper problem than methodology.

  2. What are the regulatory constraints?
     Strict compliance, audit trail → Waterfall or hybrid with formal gates
     Flexible, outcome-focused      → Agile, Kanban
     → Agile and compliance are not mutually exclusive. Agile with
       documentation gates works. Pure Agile without audit trail in a
       regulated environment does not.

  3. What is the team experienced with?
     Experienced Agile practitioners → Agile, scaled Agile
     Traditional PM background      → Waterfall or hybrid (transition gradually)
     Mixed                          → Hybrid with clear boundaries
     → The best methodology the team can't execute is worse than a
       good-enough methodology they understand (*Principle 6*).

  4. How large is the effort?
     Single team (5-8)              → Scrum, Kanban
     Multiple teams, shared goal    → SAFe, LeSS, Nexus
     Multiple independent projects  → Portfolio management
     → Don't scale until you've proven the approach at team level first.

  5. What governance is proportional?
     Low risk, small team           → lightweight (standups, demo, retro)
     High risk, regulatory          → formal gates, steering committee, change board
     → Scale governance to risk, not to organizational habit (*Principle 7*).
```

## Decision Engine: Prioritization

```
  1. What is the strategic alignment?
     Does this initiative serve organizational strategy?
     → If no, deprioritize regardless of other factors.

  2. What is the value vs effort?
     High value, low effort → do first (quick wins)
     High value, high effort → plan carefully (big bets)
     Low value, low effort  → fill-in (if capacity allows)
     Low value, high effort → don't do (money pit)

  3. What are the dependencies?
     Does this block other high-value work?
     → If yes, priority rises regardless of its own value.

  4. What is the opportunity cost?
     Every project you fund is a project you're not funding.
     → Ask: "What am I not doing by doing this?"

  5. Apply a framework consistently:
     Simple prioritization → MoSCoW (Must/Should/Could/Won't)
     Quantitative comparison → RICE (Reach × Impact × Confidence / Effort)
     Multiple stakeholder perspectives → Weighted scoring
     → The framework matters less than using one consistently.
```

---

## Stage Gates: The Project Boundary System

Decisions are enforced at phase transitions. Each gate checks different things.

```
  Discover ──▶ [GATE 1] ──▶ Define ──▶ [GATE 2] ──▶ Plan ──▶ [GATE 3] ──▶ Deliver
                Business       Scope      Plan           WBS       Execution
                case           agreed     credible       complete  authorized
                valid

  Deliver ──▶ [GATE 4] ──▶ Control ──▶ [GATE 5] ──▶ Close
               Milestone      Progress     Acceptance
               review         on track     confirmed
```

| Gate | Transition | Key questions | On failure |
|------|-----------|---------------|------------|
| **Gate 1** | Discover → Define | Is the business case viable? Are stakeholders identified? Do we understand the real problem? | Stop or redirect. Don't define a project for a problem you haven't understood |
| **Gate 2** | Define → Plan | Is scope agreed? Are constraints clear? Is methodology selected? Is feasibility confirmed? | Go back to Discover. Unclear scope produces undeliverable plans |
| **Gate 3** | Plan → Deliver | Is the plan credible? Are resources allocated? Are risks identified? Is the communication plan ready? | Revise plan. Starting execution with an incredible plan wastes everyone's time |
| **Gate 4** | Deliver (milestones) | Are deliverables meeting acceptance criteria? Is the project on track? Are risks being managed? | Re-baseline, reduce scope, or escalate. Don't continue against a fiction |
| **Gate 5** | Control → Close | Are deliverables accepted? Is knowledge transferred? Are lessons captured? | Complete acceptance. Closing without formal acceptance creates orphaned projects |

In Agile, these gates compress: Gate 1-2 happen at product discovery. Gate 3 happens at sprint planning. Gate 4 happens at sprint review. Gate 5 happens at release.

---

## Cross-Cutting Concept Threads

The same concept manifests differently at each lifecycle phase.

| Concept | At Discover | At Define | At Plan | At Deliver | At Control | At Adapt |
|---------|------------|----------|--------|-----------|-----------|---------|
| **Stakeholders** | Identify, map power/interest, understand motivations | Engage, align on scope, set expectations | Communication plan, engagement strategy | Manage, communicate, remove blockers | Report status, escalate, re-align | Retrospective feedback, relationship review |
| **Risk** | Identify landscape risks, feasibility unknowns | Constraints, assumptions, feasibility risks | Risk register, response plans, contingency | Execute responses, monitor triggers | Track, reassess, identify new risks | Lessons learned, pattern analysis |
| **Scope** | Define the actual problem, separate symptoms from causes | Scope statement, WBS, acceptance criteria | Detailed scope, deliverables, dependencies | Manage via change control | Validate deliverables, control changes | Review scope decisions, what was absorbed |
| **Quality** | Define real success criteria (stated + unstated) | Quality standards, Definition of Done | Quality plan, metrics, testing strategy | Quality assurance, prevention over inspection | Quality control, testing, acceptance | Process improvement, quality trend analysis |
| **Communication** | Understand audience needs and preferences | Communication matrix, reporting cadence | Detailed communication plan | Execute communication, status reporting | Escalation, dashboards, decision support | Share lessons, cross-project learning |
| **Governance** | Identify compliance requirements, political dynamics | Governance structure, decision authority | Approval workflows, escalation paths | Change control, vendor management | Stage gate reviews, compliance checks | Governance effectiveness review |

**The Stakeholder thread**: Stakeholder management appears at every phase because stakeholders are the system boundary between the project and the organization. If the boundary is poorly managed, everything inside the project is affected regardless of how well it's executed.

**The Risk thread**: Risk shifts character across phases. At Discover, risks are unknowns about the problem itself. At Plan, risks are identified and registered. At Deliver, risks become issues that need real-time response. At Adapt, risks become patterns that inform future projects.

---

## Ripple Effect Maps

### The Scope Creep Cascade

```
  Unclear requirements at Define
    → stakeholders add requests during Deliver ("while we're at it...")
      → each addition seems small, collectively they're large
        → timeline and budget don't adjust (nobody wants to be the one to say no)
          → quality drops as team rushes to fit everything in
            → deliverables don't meet original acceptance criteria
              → stakeholder dissatisfaction despite getting "more"
                → more changes requested to fix → cycle accelerates
```

*Patterns involved*: Scope Absorption, Trust-Velocity (trust erodes from quality drop), Stakeholder Drift (original agreement forgotten).
*Break the cycle at*: Define phase. Explicit scope statement with change control process. Every addition gets impact-assessed. "Yes, and here's what it costs" instead of "yes" or "no."

### The Trust Erosion Cascade

```
  Team misses a deadline
    → PM reports "slight delay" instead of full impact (bad news withheld)
      → stakeholders discover the real impact later → surprise
        → trust drops → oversight increases (more reports, more meetings)
          → team spends more time reporting, less time delivering
            → next deadline missed → PM minimizes again → cycle deepens
              → eventually: external review, project recovery, or cancellation
```

*Patterns involved*: Trust-Velocity (negative loop), Governance Accumulation (oversight adds overhead).
*Break the cycle at*: The first missed deadline. Report honestly, with impact and recovery plan. Re-baseline if the plan is no longer realistic. Commit to a small deliverable and hit it. Trust is rebuilt through demonstrated reliability (*Principle 9*).

### The Governance Overhead Cascade

```
  Project failure or audit finding
    → organization adds new approval gate / review / report
      → next project takes longer due to new overhead
        → another failure in a different area → another gate added
          → governance overhead consumes 40%+ of project capacity
            → teams work around governance (shadow processes)
              → governance loses actual visibility
                → more governance added to compensate → cycle continues
```

*Patterns involved*: Governance Accumulation (pure instance), Trust-Velocity (overhead slows delivery).
*Break the cycle at*: Periodic governance audit. For each gate: does this actually reduce risk, or does it only add delay? Design governance for outcomes, not compliance theater. Track cycle time to make overhead visible (*Principle 10*).

### The Hero Dependency Cascade

```
  Key person holds critical project knowledge (bus factor = 1)
    → person is overloaded (single point of dependency for all decisions)
      → person leaves, is reassigned, or burns out
        → institutional knowledge lost
          → remaining team guesses or re-discovers from scratch
            → decisions slow, errors increase, delivery stalls
              → stakeholder confidence drops → oversight increases
                → more process added → remaining team members leave
```

*Patterns involved*: Stakeholder Drift (unstated dependency), Trust-Velocity (delivery stalls), Governance Accumulation (process added as substitute for knowledge).
*Break the cycle at*: Discover/Define phases. Identify knowledge concentration early. Cross-train. Document decisions and rationale, not just outcomes. If onboarding a new team member takes months, the project is too opaque.

---

## Key References

| Topic | Doc |
|-------|-----|
| Problem solving, mental models, biases, decision-making | [Critical Thinking](critical_thinking.md) |
| Project fundamentals, scope, risk, stakeholders, quality, cost, governance | [Project Management](../concepts/project_management.md) |
| IT project types, technical debt, vendor management, DevOps integration | [IT Project Management](../concepts/itpm.md) |
| Agile, Scrum, Kanban, Waterfall, hybrid, scaling frameworks, Lean | [Methodologies](../concepts/methodologies.md) |
| Product strategy, discovery, roadmapping, metrics, prioritization | [Product Management](../concepts/product_management.md) |
| SDLC phases, requirements, testing, deployment, maintenance | [SDLC](../concepts/sdlc.md) |
| Consulting, stakeholder engagement, facilitation, negotiation, influence | [Consulting & Engagement](../concepts/consulting_engagement.md) |
| Roadmap types, development process, stakeholder communication | [Roadmap](../concepts/roadmap.md) |
| Reporting types, Power BI, dashboards, update frequencies | [Reporting](../concepts/reporting.md) |
| Industry standards, frameworks, legislation, certifications | [Standards](../../_resources/standards.md) |

> **Strategy docs**: [Approach](../../_strategy/01_shared_approach.md) · [Complexity](../../_strategy/02_shared_complexity.md) · [Critical Thinking](../../_strategy/03_shared_critical_thinking.md) · [Systems Thinking](../../_strategy/04_shared_systems_thinking.md)
