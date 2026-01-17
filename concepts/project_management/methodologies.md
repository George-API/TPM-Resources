# Project Management Methodologies

**Scope**: Project management frameworks, methodologies, and approaches (Agile, Scrum, Kanban, Waterfall, hybrid, scaling frameworks).

**Purpose**: Use this for methodology selection, implementation, and best practices. For general PM fundamentals, see [General Project Management](general.md). For IT-specific patterns, see [IT Project Management](itpm.md).

## Table of Contents

- [1. Methodology Selection](#1-methodology-selection)
- [2. Agile Principles](#2-agile-principles)
- [3. Scrum Framework](#3-scrum-framework)
- [4. Kanban](#4-kanban)
- [5. Waterfall](#5-waterfall)
- [6. Hybrid Approaches](#6-hybrid-approaches)
- [7. Scaling Frameworks](#7-scaling-frameworks)
- [8. Lean & DevOps](#8-lean--devops)
- [9. Enterprise Methodologies](#9-enterprise-methodologies)

---

## 1. Methodology Selection

### Methodology Comparison

| Aspect | Waterfall | Agile | Kanban | Hybrid |
|--------|-----------|-------|--------|--------|
| **Planning** | Detailed upfront | Iterative | Continuous | Phased |
| **Change** | Difficult | Easy | Easy | Moderate |
| **Delivery** | End of project | Incremental | Continuous | Phased |
| **Risk** | Late discovery | Early feedback | Continuous | Balanced |
| **Best for** | Fixed scope, clear requirements | Changing requirements, innovation | Operations, maintenance | Complex projects |

### Selection Criteria

- **Requirements stability**: stable → Waterfall; changing → Agile
- **Project size**: small → Agile/Kanban; large → Waterfall/hybrid
- **Team experience**: experienced → Agile; new → Waterfall/hybrid
- **Regulatory constraints**: strict → Waterfall/hybrid; flexible → Agile
- **Customer involvement**: high → Agile; low → Waterfall

### Modern Approach

- **Methodology fit**: choose methodology that fits context, not one-size-fits-all
- **Hybrid common**: most organizations use hybrid approaches (enterprise reality)
- **Continuous improvement**: evolve methodology based on learnings
- **Enterprise adoption**: phased rollout, pilot programs, organizational change management

---

## 2. Agile Principles

### Agile Manifesto Values

1. **Individuals and interactions** over processes and tools
2. **Working software** over comprehensive documentation
3. **Customer collaboration** over contract negotiation
4. **Responding to change** over following a plan

### Agile Principles (12)

1. Customer satisfaction through early, continuous delivery
2. Welcome changing requirements (competitive advantage)
3. Deliver working software frequently (weeks, not months)
4. Business and developers work together daily
5. Build projects around motivated individuals; trust and support them
6. Face-to-face conversation is most effective
7. Working software is primary measure of progress
8. Sustainable pace (maintainable long-term)
9. Continuous attention to technical excellence
10. Simplicity (maximize work not done)
11. Self-organizing teams (best architectures/requirements emerge)
12. Regular reflection and adaptation

### Agile Mindset

- **Empirical process**: inspect and adapt (vs defined process)
- **Value focus**: deliver highest value first
- **Collaboration**: cross-functional teams, customer involvement
- **Transparency**: visible work, honest status, shared understanding

---

## 3. Scrum Framework

### Scrum Roles

- **Product Owner**: owns product backlog, prioritizes, defines acceptance criteria
- **Scrum Master**: facilitates, removes blockers, ensures Scrum process
- **Development Team**: self-organizing, cross-functional, delivers increments

### Scrum Events

- **Sprint Planning**: plan sprint work, select backlog items, define sprint goal (time-boxed: 2-4 hours for 2-week sprint)
- **Daily Scrum**: 15-minute sync (what done, what next, blockers)
- **Sprint Review**: demonstrate increment, gather feedback (time-boxed: 2-4 hours)
- **Sprint Retrospective**: reflect, identify improvements, plan actions (time-boxed: 1-2 hours)
- **Sprint**: fixed-length iteration (typically 1-4 weeks)

### Scrum Artifacts

- **Product Backlog**: ordered list of work (user stories, features, bugs)
- **Sprint Backlog**: selected work for sprint, plan for delivering increment
- **Increment**: sum of all completed work in sprint (potentially shippable)

### Scrum Best Practices

- **Sprint goal**: clear, shared goal for sprint (not just list of tasks)
- **Definition of Done**: shared understanding of completion criteria
- **Backlog refinement**: regularly refine backlog (estimates, acceptance criteria)
- **Velocity**: measure team capacity, use for planning (not for comparison)
- **Scrum Master role**: servant leader, not task manager

---

## 4. Kanban

### Kanban Principles

1. **Visualize work**: board with columns (To Do, In Progress, Done)
2. **Limit WIP**: work in progress limits prevent overload
3. **Manage flow**: optimize flow of work (reduce bottlenecks)
4. **Make policies explicit**: clear rules, definitions, processes
5. **Improve collaboratively**: continuous improvement based on data

### Kanban Practices

- **Kanban board**: visualize workflow stages, work items, blockers
- **WIP limits**: set limits per column/team, enforce to prevent overload
- **Flow metrics**: lead time, cycle time, throughput, work item age
- **Classes of service**: expedite, fixed delivery date, standard, intangible

### Kanban vs Scrum

- **Scrum**: time-boxed sprints, roles, ceremonies, fixed scope
- **Kanban**: continuous flow, no roles, no ceremonies, flexible scope
- **Hybrid**: Scrumban (Scrum structure + Kanban flow)

### Kanban Best Practices

- **Start with current process**: visualize what you do, then improve
- **WIP limits**: start conservative, adjust based on flow
- **Flow metrics**: measure lead time, identify bottlenecks
- **Continuous improvement**: regular retrospectives, experiment with changes

---

## 5. Waterfall

### Waterfall Phases

1. **Requirements**: gather and document all requirements
2. **Design**: system design, architecture, detailed design
3. **Implementation**: development, coding, unit testing
4. **Testing**: integration testing, system testing, UAT
5. **Deployment**: production deployment, go-live
6. **Maintenance**: support, bug fixes, enhancements

### Waterfall Characteristics

- **Sequential phases**: phases completed in order, gates between phases
- **Detailed planning**: comprehensive upfront planning
- **Documentation heavy**: extensive documentation at each phase
- **Change control**: formal change control process (changes are expensive)

### When to Use Waterfall

- **Fixed requirements**: requirements are clear, stable, unlikely to change
- **Regulatory projects**: strict compliance, audit trails, documentation required
- **Large projects**: complex, multi-year projects with many dependencies
- **Predictable scope**: well-understood domain, proven technology

### Waterfall Best Practices

- **Phase gates**: clear exit criteria for each phase
- **Change control**: formal process for managing changes
- **Documentation**: maintain comprehensive documentation
- **Risk management**: identify risks early, plan mitigation

---

## 6. Hybrid Approaches

### Common Hybrid Patterns

- **Waterfall planning, Agile execution**: detailed planning, iterative delivery
- **Agile development, Waterfall deployment**: Agile for dev, Waterfall for release
- **Scrumban**: Scrum structure (sprints, roles) + Kanban flow (WIP limits, continuous)
- **Agile for software, Waterfall for infrastructure**: different methodologies for different work types

### Hybrid Best Practices

- **Clear boundaries**: define where each methodology applies
- **Consistent communication**: ensure teams understand hybrid approach
- **Flexibility**: adjust hybrid approach based on learnings
- **Avoid confusion**: don't mix methodologies in ways that create confusion

---

## 7. Scaling Frameworks

### SAFe (Scaled Agile Framework)

- **Levels**: Team, Program, Large Solution, Portfolio
- **Agile Release Train (ART)**: 50-125 people, aligned to value stream
- **PI Planning**: Program Increment planning (quarterly planning event)
- **Roles**: Product Owner, Scrum Master, Release Train Engineer, Product Manager
- **Artifacts**: Program Backlog, Features, Epics, Roadmap

### LeSS (Large-Scale Scrum)

- **LeSS**: single Product Owner, single Product Backlog, multiple teams
- **LeSS Huge**: multiple Product Owners, Area Product Backlogs, Requirement Areas
- **Principles**: transparency, more with less, whole product focus
- **Events**: Sprint Planning (Part 1: all teams, Part 2: per team), Overall Retrospective

### Nexus

- **Nexus Integration Team**: ensures integration across teams
- **Nexus Sprint**: aligned sprints across teams, shared sprint goal
- **Nexus Events**: Nexus Sprint Planning, Nexus Daily Scrum, Nexus Sprint Review, Nexus Retrospective
- **Artifacts**: Nexus Sprint Backlog, Integrated Increment, Nexus Goal

### Scaling Best Practices

- **Start small**: prove Agile at team level before scaling
- **Value streams**: organize around value, not functions
- **Dependencies**: minimize dependencies, manage remaining ones explicitly
- **Coordination**: regular sync events, clear communication channels
- **Enterprise adoption**: executive sponsorship, organizational change, training, coaching

---

## 8. Lean & DevOps

### Lean Principles

- **Eliminate waste**: remove non-value-adding activities
- **Amplify learning**: short feedback loops, continuous learning
- **Decide as late as possible**: defer decisions until last responsible moment
- **Deliver as fast as possible**: reduce cycle time, deliver value quickly
- **Empower team**: give team authority, responsibility, accountability
- **Build integrity in**: quality from start, not inspection
- **See the whole**: systems thinking, optimize whole, not parts

### DevOps Culture

- **Collaboration**: Dev and Ops work together, shared goals
- **Automation**: automate repetitive work (CI/CD, IaC, testing)
- **Measurement**: measure everything (deployment frequency, lead time, MTTR)
- **Sharing**: share knowledge, tools, practices across teams
- **Continuous improvement**: learn from failures, improve continuously

### Lean + DevOps Practices

- **Value stream mapping**: identify waste, optimize flow
- **Continuous delivery**: deploy frequently, small batches
- **Infrastructure as Code**: version control infrastructure, automate provisioning
- **Monitoring**: observability, alerting, dashboards
- **Blame-free culture**: failures are learning opportunities

---

## 9. Enterprise Methodologies

### Disciplined Agile (DA)

- **Hybrid approach**: combines Agile, Lean, DevOps, traditional PM
- **Context-driven**: choose practices that fit context, not prescriptive
- **Scaling**: team → value stream → portfolio levels
- **Roles**: team lead (vs Scrum Master), product owner, architecture owner, team member
- **Enterprise fit**: designed for enterprise contexts, governance, compliance

### PRINCE2 (Projects IN Controlled Environments)

- **Process-based**: 7 processes (starting, directing, initiating, controlling, managing, delivering, closing)
- **Theme-based**: 7 themes (business case, organization, quality, plans, risk, change, progress)
- **Principle-based**: 7 principles (continued business justification, learn from experience, defined roles, manage by stages, manage by exception, focus on products, tailor to environment)
- **Enterprise adoption**: common in UK/Europe, government projects, large enterprises
- **Governance**: strong governance structure, stage gates, decision points

### PMBOK Guide (PMI)

- **Knowledge areas**: integration, scope, schedule, cost, quality, resources, communications, risk, procurement, stakeholders
- **Process groups**: initiating, planning, executing, monitoring & controlling, closing
- **Standards**: PMI standards, certifications (PMP, CAPM), best practices
- **Enterprise alignment**: widely recognized, integrates with other frameworks
- **Tailoring**: adapt processes to project needs, organizational context

### Enterprise Methodology Selection

- **Organizational maturity**: assess PM maturity, select appropriate methodology
- **Project characteristics**: size, complexity, risk, regulatory requirements
- **Cultural fit**: align with organizational culture, change readiness
- **Hybrid reality**: most enterprises use hybrid approaches, not pure methodologies

### Best Practices

- **Tailor methodologies**: adapt to context, don't follow blindly
- **Start simple**: begin with core practices, evolve based on learnings
- **Organizational change**: methodology adoption requires change management
- **Continuous improvement**: evolve practices, measure effectiveness, adapt

---

> **Note**: For general PM fundamentals, see [General Project Management](general.md). For IT-specific patterns, see [IT Project Management](itpm.md).

