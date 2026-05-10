# Software Development — Systems Thinking Reference

Software is a single system: requirements become code, code becomes deployable artifacts, artifacts become running services, running services generate feedback, feedback shapes new requirements. Every concept in this directory—SOLID, bounded contexts, CI/CD, STRIDE, the testing pyramid—is a tool for making one part of that cycle reliable. They all connect through one lifecycle and four patterns unique to how software behaves.

```
  ┌──────────────────────────────────────────────────────────────────┐
  │                  THE SOFTWARE LIFECYCLE                          │
  │                                                                  │
  │ Understand ──▶ Model ──▶ Implement ──▶ Verify ──▶ Ship ──▶ Run│
  │       ▲                                                    │     │
  │       └─────────────────── feedback ───────────────────────┘     │
  └──────────────────────────────────────────────────────────────────┘
```

| Phase | What happens | Key docs |
|-------|-------------|----------|
| **Understand** | Define the problem, identify constraints, trace data flow, know failure modes | [Critical Thinking](critical_thinking.md) |
| **Model** | Architecture style, bounded contexts, domain model, API contracts, threat model | [Architecture](../concepts/architecture.md), [Design](../concepts/design.md) |
| **Implement** | Write code, apply patterns, manage dependencies, review | [Design](../concepts/design.md), [Languages](../languages/) |
| **Verify** | Test pyramid, security scans, quality gates, contract tests | [Design](../concepts/design.md), [DevSecOps](../concepts/devsecops.md) |
| **Ship** | CI/CD pipeline, IaC, deployment strategy, release | [DevOps](../concepts/devops.md), [Azure DevOps](../concepts/azure_devops.md) |
| **Run** | Observe, respond, measure SLOs, postmortem, simplify | [Architecture](../concepts/architecture.md), [DevOps](../concepts/devops.md) |

Security is not a phase. It is a dimension that runs through every phase: threat modeling at Model, secure coding at Implement, SAST/SCA/DAST at Verify, secrets management at Ship, observability at Run. If security only appears as a gate before release, it catches problems at maximum cost.

---

## Governing Principles

These 12 rules explain 80% of correct software engineering decisions. When in doubt, check here first.

1. **Understand before coding.** Trace the data flow. Identify invariants. Know the failure modes. Code that works by coincidence breaks under conditions you didn't anticipate. → *Visibility Pattern*
2. **Dependencies point inward.** Business logic depends on nothing external. Frameworks, databases, and UI depend on business logic. This is the shared core of Clean, Hexagonal, and Onion architecture, and the reason Dependency Inversion is the most important SOLID principle.
3. **YAGNI until proven otherwise.** Don't build for hypothetical future needs. Every abstraction, layer, and indirection has cognitive and maintenance cost. Build when you have evidence, not speculation. → *Abstraction Pattern*
4. **Prefer boring technology.** Proven, well-understood tools reduce risk. Save your complexity budget for the parts that actually differentiate. Résumé-driven development and hype-driven architecture are the same anti-pattern.
5. **The test pyramid is a budget.** Many fast unit tests, fewer integration tests, fewest E2E tests. Inverting this pyramid (many E2E, few unit) produces slow feedback, flaky CI, and test suites nobody trusts. → *Shift-Left Pattern*
6. **Small changes, shipped frequently.** Small PRs are easier to review, test, deploy, and rollback. Large changes compound risk at every stage. Feature flags and trunk-based development make this possible. → *Accumulation Pattern*
7. **Measure before optimizing.** Profiling reveals that the bottleneck is almost never where you think. Gut instinct about performance is unreliable. Profile first, every time.
8. **Design for failure.** Networks fail, disks fill, dependencies go down, inputs are malformed. Resilience patterns (circuit breaker, bulkhead, retry with backoff, timeout) exist because assuming success is fragile.
9. **Conway's Law is not optional.** Team structure determines system architecture. If you want modular services, organize modular teams. If you want a monolith, a single team is fine. Fighting Conway's Law always loses.
10. **Shift security left.** Catching a vulnerability at code time costs orders of magnitude less than in production. SAST in CI, dependency scanning on every build, threat modeling at design. Security is a continuous signal, not a gate. → *Shift-Left Pattern*
11. **Observability is not monitoring.** Monitoring checks known failure modes. Observability lets you ask new questions of a system you didn't anticipate. Metrics + logs + traces, correlated, with SLO-based alerting.
12. **Feedback loops determine quality.** Fast CI, automated tests, code review, observability, postmortems—each one tightens the loop between action and consequence. Slow loops produce slow learning. → *Feedback Loop Pattern*

---

## Repeating Patterns

Four structures unique to how software behaves recur across every topic.

### 1. The Abstraction Pattern

Software is layers of abstraction. Every architectural style, design principle, and pattern is a decision about where to draw abstraction boundaries and which direction dependencies flow.

```
  Too little abstraction          Right abstraction           Too much abstraction
  ─────────────────────          ─────────────────           ─────────────────────
  Duplication                    Reuse without coupling       Over-engineering
  Tight coupling                 Clear interfaces             Framework-for-one
  Change ripples everywhere      Change is localized          Nobody can trace the flow
  Fast to start, slow to change  Balanced                     Slow to start, slow to change
```

**This is the single thread through all design principles**:
- **SOLID** = five rules for drawing abstractions that minimize the cost of change
- **SRP**: one reason to change → minimal blast radius
- **OCP**: extend without modifying → existing code doesn't break
- **DIP**: depend on abstractions → swap implementations without touching business logic
- **Clean/Hexagonal/Onion**: business logic at center, infrastructure at the edge, dependencies inward
- **DDD Bounded Contexts**: domain model boundaries that prevent model pollution across teams
- **Microservice boundaries**: deployment independence requires database-per-service, clear API contracts

**Decision rule**: If you can't explain why an abstraction exists and what change it protects against, it's accidental complexity. Remove it.

### 2. The Shift-Left Pattern

The cost of fixing a problem grows exponentially with how late in the lifecycle you find it. Moving verification earlier catches problems cheaper.

```
  Cost to fix:

  Understand   Model    Implement   Verify    Ship      Run
     $1         $5        $10        $50      $200     $1000+
     │          │          │          │         │         │
     ▼          ▼          ▼          ▼         ▼         ▼
  Design      Code       Unit      Integration Deploy  Production
  review      review     test      test        smoke    incident
                                                test
```

**Instances**:
- **Security**: Threat model at Model → secure coding at Implement → SAST at Verify → secrets at Ship → observability at Run. Each step left catches a class of issue the next step can't.
- **Testing**: TDD catches logic errors at Implement. Integration tests catch interface errors at Verify. E2E tests catch workflow errors before Ship. Each layer catches what the previous missed, but at higher cost.
- **Architecture**: A wrong architectural decision caught at Model costs a design change. Caught at Run, it costs a rewrite.
- **Quality**: Linters and formatters at Implement catch issues that code review at Verify would spend human time on. Automate what machines can check; save human review for what requires judgment.

### 3. The Feedback Loop Pattern

Outcomes feed back into causes. Fast loops produce fast learning. Slow loops produce slow learning. Broken loops produce no learning.

```
  Fast loop:   Code ──▶ CI (minutes) ──▶ test result ──▶ fix ──▶ commit
  
  Slow loop:   Code ──▶ manual QA (days) ──▶ bug report ──▶ context lost ──▶ fix
  
  Broken loop: Code ──▶ no tests ──▶ deploy ──▶ user finds bug ──▶ blame
```

**Positive loops** (amplifying):
- Fast CI → developers trust the pipeline → they run it more → quality rises
- Small PRs → fast reviews → fast merges → more small PRs
- Good observability → fast incident detection → fast resolution → confidence to ship

**Negative loops** (eroding):
- Flaky tests → developers ignore test failures → bugs ship → more firefighting
- Alert fatigue → real alerts missed → incidents grow → more alerts added
- Tech debt → slower delivery → shortcuts to compensate → more debt

**Break a negative loop** by addressing the loop mechanism, not the symptom. Fix the flaky tests. Reduce the alerts. Pay down the debt.

### 4. The Resilience Pattern

Distributed systems fail in partial, unpredictable ways. Resilience patterns exist because failure is a certainty, not a possibility.

```
  Dependency stack (if bottom fails, everything above breaks):

  ┌────────────────────────────┐
  │  User-facing feature       │ ← depends on API
  ├────────────────────────────┤
  │  API / Service             │ ← depends on dependencies
  ├────────────────────────────┤
  │  Dependencies (DB, cache,  │ ← depends on infrastructure
  │   external APIs, queues)   │
  ├────────────────────────────┤
  │  Infrastructure (cloud,    │ ← depends on network
  │   containers, runtime)     │
  ├────────────────────────────┤
  │  Network (DNS, TLS, LB)    │
  └────────────────────────────┘
```

**Resilience is not hoping the dependency stack holds. It is designing for when it doesn't.**

| Pattern | What it does | When to use |
|---------|-------------|-------------|
| **Circuit breaker** | Stops calling a failing dependency; fails fast instead of waiting | External service calls, database connections |
| **Bulkhead** | Isolates resources so one failure doesn't consume all capacity | Thread pools, connection pools, service instances |
| **Retry with backoff** | Handles transient failures; exponential backoff prevents thundering herd | Network calls, queue consumers |
| **Timeout** | Prevents indefinite blocking on a slow dependency | Every external call should have a timeout |
| **Health checks** | Liveness (is it running?) and readiness (can it serve traffic?) | Container orchestration, load balancers |
| **Graceful degradation** | Returns partial results or cached data instead of failing completely | User-facing features with non-critical dependencies |

---

## Never Rules & First-Step Rules

### Never Rules

| # | Rule | Because |
|---|------|---------|
| 1 | Never code before understanding the problem | Solving the wrong problem is negative value |
| 2 | Never optimize without profiling | The bottleneck is never where you think (*Principle 7*) |
| 3 | Never add an abstraction without evidence of need | YAGNI. Every layer has cognitive cost (*Principle 3*) |
| 4 | Never adopt a technology because it's trending | Fit to constraints, not hype (*Principle 4*) |
| 5 | Never silence an exception without investigating | `catch {}` hides systemic issues → *Feedback Loop* breaks |
| 6 | Never share a database between services | Shared database = distributed monolith. Database-per-service or don't distribute |
| 7 | Never commit secrets to source control | Once in git history, effectively public. Secret managers exist |
| 8 | Never skip tests to meet a deadline | You're borrowing time from your future self at compound interest → *Accumulation* |
| 9 | Never deploy without the ability to rollback | One-way deployments turn every release into a high-stakes gamble |
| 10 | Never ignore a failing test | A test you ignore is a bug you've accepted. Either fix the test or fix the code |

### First-Step Rules

| Situation | First step | Why |
|-----------|-----------|-----|
| Unfamiliar codebase | Find the entry point. Trace one request end-to-end | Understand before acting (*Principle 1*) |
| "The API is slow" | Profile. Find the actual bottleneck | Could be N+1 query, missing index, network hop, or wrong expectation |
| Bug report | Reproduce reliably first | Can't verify a fix for a bug you can't reproduce |
| Architecture decision | List constraints: scale, team size, compliance, timeline, legacy | Constraints determine architecture, not preferences |
| Choosing a framework | Define requirements first, then evaluate fit | Start with the problem, not the tool (*Never Rule 4*) |
| Production incident | Stabilize first, investigate after | Cynefin Chaotic: Act-Sense-Respond |
| Inheriting legacy code | Run the tests. Check coverage. Read `git blame` | Tests reveal intent; blame reveals history |
| Recurring failures | Look for the systemic cause, not the individual | Recurring = systemic. Fix the system (*Feedback Loop*) |
| Microservices question | Ask: how many teams, how many domains? | One team, one domain → monolith. Conway's Law (*Principle 9*) |

---

## Boundary Distinctions

Confusable concepts with the single property that separates them.

| Pair | Distinguishing property | Decision rule |
|------|------------------------|---------------|
| **Monolith vs microservices** | Deployment independence | Small team, single domain → monolith. Multiple teams, independent domains → services. Never microservices for "scalability" alone |
| **Modular monolith vs microservices** | Network boundary | Same process, enforced module boundaries → modular monolith. Separate processes, separate databases → microservices |
| **Library vs framework** | Inversion of control | You call a library. A framework calls you |
| **CI vs CD** | What is automated | CI: build + test on every commit. CD: automated deployment to staging/production |
| **SAST vs SCA vs DAST** | What is scanned, when | SAST: your code, at build. SCA: your dependencies, at build. DAST: running application, at deploy |
| **Authentication vs authorization** | What is verified | AuthN: who are you? (identity). AuthZ: what can you do? (permissions). Always AuthN first |
| **Event sourcing vs CQRS** | Core idea | Event sourcing: store events, derive state. CQRS: separate read/write models. Often used together, but independent concepts |
| **Saga vs 2PC** | Coordination model | 2PC: distributed lock, strong consistency, blocking. Saga: compensating transactions, eventual consistency, non-blocking |
| **Observability vs monitoring** | Known vs unknown | Monitoring: dashboards for known failure modes. Observability: ability to ask new questions you didn't predict |
| **Unit vs integration test** | What is isolated | Unit: single component, mocked dependencies, fast. Integration: real dependencies interacting, slower |
| **Feature flag vs branch** | Where divergence lives | Flag: runtime, gradual rollout, instant rollback. Branch: code-level, merge required, harder to reverse |
| **Horizontal vs vertical scaling** | How capacity is added | Horizontal: more instances (requires stateless design). Vertical: bigger instance (has ceiling) |

---

## Decision Engine: Architecture

```
  1. What is the problem?
     Requirements: functional, scale, latency, consistency, compliance.
     → If unclear, stop. You'll build the wrong thing (*Principle 1*).

  2. What are the constraints?
     Team size, skill, timeline, budget, existing systems.
     → A monolith is correct for 1 team. Microservices are correct
       for 8 teams. Neither is universally "better."

  3. What is the domain structure?
     Single bounded context → monolith or modular monolith
     Multiple bounded contexts, single team → modular monolith
     Multiple contexts, multiple teams → service-oriented
     → Conway's Law: team structure = system structure (*Principle 9*).

  4. What consistency model?
     Strong consistency required → synchronous, ACID, single DB
     Eventual consistency tolerable → async, events, saga pattern
     → Most systems tolerate more eventual consistency than
       architects initially assume. Don't distribute state
       unless you must.

  5. Synchronous or asynchronous?
     Request needs immediate response → sync (REST, gRPC)
     Fire-and-forget or long-running → async (events, queues)
     → Default to sync. Add async when coupling or latency requires it.

  6. Which architectural style?
     Simple CRUD, small team → Layered
     Complex domain, multiple interfaces → Hexagonal / Clean
     Separate read/write optimization → CQRS
     Full audit trail, time-travel → Event Sourcing
     High-throughput decoupled producers/consumers → Event-driven
```

## Decision Engine: Language Selection

```
  Use the highest-level language that doesn't hide risks you care about.
  Drop lower only when correctness, scale, or performance demands it.

  ┌─────────────────────────────────────────────────────────────────────┐
  │ Higher abstraction ◄──────────────────────────► Lower abstraction   │
  │ Python → JS/TS → Go/C#/Java → Rust → C++ → C                        │
  └─────────────────────────────────────────────────────────────────────┘

  Data analysis, ML, scripting, automation        → Python
  Large-scale data engineering (Spark)             → PySpark
  Statistical modeling, research                   → R
  Frontend and full-stack web                      → JavaScript / TypeScript
  DevOps, infrastructure, cloud-native backends    → Go
  Azure enterprise platforms, APIs, integrations   → C# / .NET
  Large enterprise platforms, JVM ecosystem        → Java
  Memory-safe systems, security-critical           → Rust
  Performance-critical, real-time, low-level       → C / C++

  If you see: "which language?"
  Ask first: what are the constraints? (team skill, ecosystem,
  performance, safety, deployment target)
```

---

## The Verification Pipeline

Quality is enforced at boundaries, not patched at the end. The CI/CD pipeline is the software equivalent of a quality gate system.

```
  Code ──▶ [GATE 1] ──▶ Build ──▶ [GATE 2] ──▶ Staging ──▶ [GATE 3] ──▶ Prod
            Lint          Compile    Unit+Integ   Deploy      E2E          Deploy
            Format        Package    SAST/SCA     Contract    Smoke        Monitor
            Secrets scan             Coverage     Perf test   Canary       SLOs
```

| Gate | Phase | Checks | Feedback speed |
|------|-------|--------|----------------|
| **Gate 1** (Pre-build) | Implement | Lint, format, secrets scan, commit conventions | Seconds (IDE + pre-commit) |
| **Gate 2** (Post-build) | Verify | Unit tests, integration tests, SAST, SCA, coverage threshold | Minutes (CI pipeline) |
| **Gate 3** (Pre-production) | Ship | E2E tests, contract tests, smoke tests, canary analysis | Minutes to hours |
| **Runtime** | Run | SLO monitoring, error budgets, alerting, distributed tracing | Continuous |

```
  The Testing Pyramid (budget your tests this way):

         /    E2E    \        ← few, slow, expensive, high confidence
        / Integration \       ← moderate count, moderate speed
       /   Unit Tests   \     ← many, fast, cheap, catch most bugs
```

Inverting this pyramid (many E2E, few unit) produces slow CI, flaky pipelines, and test suites that nobody trusts → *Feedback Loop Pattern* turns negative.

---

## Cross-Cutting Concept Threads

The same idea manifests differently at each level of the system.

| Concept | At Code Level | At Service Level | At System Level |
|---------|--------------|-----------------|-----------------|
| **Abstraction** | Interfaces, SOLID, DIP | Bounded contexts, API contracts | Architecture style (Clean, Hex, Onion) |
| **Boundaries** | Module structure, encapsulation | Database-per-service, service mesh | Conway's Law, team topology |
| **Failure handling** | Exceptions, validation, fail-fast | Circuit breaker, bulkhead, retry | Health checks, graceful degradation, SLOs |
| **Testing** | Unit tests, TDD, property-based | Integration, contract tests | E2E, chaos engineering, load testing |
| **Security** | Input validation, secure coding | AuthN/AuthZ, API gateway, RBAC | Defense in depth, zero trust, STRIDE |
| **Feedback** | IDE errors, linters | CI test results (minutes) | Observability, postmortems (hours/days) |
| **Simplicity** | DRY, KISS, YAGNI | Avoid distributed monolith | Boring technology, minimal viable architecture |

**The Abstraction thread**: SOLID at code level, bounded contexts at service level, and architectural styles at system level are all the same idea—isolate what changes from what doesn't, point dependencies inward—applied at different scales.

**The Shift-Left thread**: Linters catch formatting at Implement (seconds). Unit tests catch logic at Verify (minutes). Integration tests catch interface errors at Verify (minutes). E2E catches workflow errors before Ship (hours). Observability catches production issues at Run (continuous). Each step right is slower and more expensive.

---

## Ripple Effect Maps

### The "Works on My Machine" Cascade

```
  Developer codes against local environment
    → local environment drifts from staging/production
      → code passes local tests, fails in CI or staging
        → "fix" adjusts for CI without understanding the difference
          → production environment differs from both
            → production deployment fails or behaves unexpectedly
              → hotfix under pressure → more drift → more incidents
```

*Patterns involved*: Feedback Loop (drift compounds), Shift-Left (environment mismatch caught late).
*Break the cycle at*: Implement phase. Containers (Docker), IaC (Terraform/Bicep), GitOps. Make environments reproducible and identical. If you can't reproduce production locally, your test results are unreliable.

### The Premature Microservices Cascade

```
  Small team adopts microservices for a single domain
    → services share a database (no real boundary enforcement)
      → schema changes require coordinating all services
        → deployments become coupled (can't deploy independently)
          → distributed system complexity without distributed system benefits
            → coordination overhead exceeds monolith overhead
              → team considers rewrite → months lost
```

*Patterns involved*: Abstraction (wrong boundaries), Resilience (distributed failure without patterns).
*Break the cycle at*: Model phase. Apply Decision Engine step 2: one team → monolith or modular monolith. If already distributed, enforce database-per-service or consolidate back.

### The Security Debt Cascade

```
  Security review deferred to "later" (pre-release gate only)
    → OWASP vulnerabilities in code accumulate (*Accumulation*)
      → dependency vulnerabilities accumulate (no SCA in CI)
        → secrets hardcoded "temporarily" in config
          → penetration test before release finds 40+ findings
            → release delayed weeks for remediation
              → rushed fixes introduce new bugs
                → next release also defers security → cycle repeats
```

*Patterns involved*: Shift-Left (security caught too late), Accumulation (vulnerabilities compound), Feedback Loop (rushed fixes create new issues).
*Break the cycle at*: Implement phase. SAST in CI, SCA on every build, secrets scanning in pre-commit hooks, threat model at design. Continuous security, not security gates.

### The Test Erosion Cascade

```
  Deadline pressure → team skips writing tests for new feature
    → coverage drops → refactoring becomes risky
      → developers avoid changing untested code
        → workarounds accumulate around fossilized code
          → new features take longer (working around, not through)
            → more deadline pressure → more skipped tests → cycle accelerates
```

*Patterns involved*: Accumulation (untested code grows), Feedback Loop (pressure → shortcuts → more pressure).
*Break the cycle at*: Never Rule 8. Budget test time into every estimate. Enforce coverage thresholds in CI gates. Treat test coverage like technical debt: track it, budget for it, never let it hit zero.

---

## Key References

| Topic | Doc |
|-------|-----|
| Problem solving, debugging, mental models, biases | [Critical Thinking](critical_thinking.md) |
| Architectural styles, DDD, microservices, distributed systems | [Architecture](../concepts/architecture.md) |
| SOLID, design patterns, testing, code review | [Design](../concepts/design.md) |
| CI/CD, IaC, deployment strategies, monitoring | [DevOps](../concepts/devops.md) |
| STRIDE, OWASP, supply chain, SAST/SCA/DAST | [DevSecOps](../concepts/devsecops.md) |
| Azure DevOps platform specifics | [Azure DevOps](../concepts/azure_devops.md) |
| Data structures, algorithms, Big O | [DSA](../concepts/dsa.md) |
| OSI model as troubleshooting lens | [OSI](../concepts/osi.md) |
| Language references | [Languages](../languages/) |
| Bicep, Terraform, Git, terminal | [Languages](../languages/) |

> **Strategy docs**: [Approach](../../_strategy/01_shared_approach.md) · [Complexity](../../_strategy/02_shared_complexity.md) · [Critical Thinking](../../_strategy/03_shared_critical_thinking.md) · [Systems Thinking](../../_strategy/04_shared_systems_thinking.md)
