# Quantitative Reference

**For TPMs, PMs, SWEs, and DMs who aren't quants.** Quick "what to do" guidance. No formulas to memorize. Translates statistical and quantitative insights into practical actions you can take today. Builds on [Integrated Reference](04_shared_systems_thinking.md) and [Quantitative Reasoning](05_shared_quantitative_reasoning.md). For deeper understanding (why + how), see the latter.

> Applies across: [Data Management](../data_management/), [Project Management](../project_management/), [Software Development](../software_development/).

```
  ┌──────────────────────────────────────────────────────────────┐
  │                    THE LIFECYCLE (from 04)                    │
  │                                                              │
  │   Understand ──▶ Analyze ──▶ Decide ──▶ Execute ──▶ Adapt     │
  │       ▲                                              │       │
  │       └──────────────── feedback ────────────────────┘       │
  └──────────────────────────────────────────────────────────────┘
```

| Phase | What to do (practical) | Why it matters |
|-------|------------------------|-----------------|
| **Understand** | Before committing: *What did similar migrations/projects/initiatives actually deliver?* Look up past outcomes. Don't rely on your gut for this one [4][5][13] | Your view of *your* project is biased. History beats intuition |
| **Analyze** | *What actually drives the outcome?* Which assumptions, if wrong, would change the answer? *Where's the bottleneck?* Map where work piles up vs where people wait [6][7][16] | Optimizing the wrong thing wastes effort. One constraint limits everything |
| **Decide** | *Weigh the trade-offs.* What's the downside if we're wrong? *When to stop analyzing?* If another week of study costs more than it could save, decide now [2][3] | Reversible = decide fast. Irreversible = invest in getting it right |
| **Execute** | *Spot real trends vs random noise.* Plot your key metrics over time - is the line drifting or bouncing? *Limit work in progress.* More items in flight = everything takes longer [8][9][12] | Reacting to every blip makes things worse. WIP is the main lever for speed |
| **Adapt** | *Compare what you predicted to what happened.* Track estimates vs actuals. Update your expectations for next time [3][5] | Without this loop, you repeat the same mistakes. Your organization never learns |

Skipping these steps produces the failures 04 documents - and the failure is often invisible until it compounds [1][4].

---

## Governing Principles

Twelve rules that help you decide and act more clearly. When in doubt, check here first.

1. **Start with what actually happened.** *In practice*: Planning a migration? Look up what similar migrations took - not what the vendor says, what *your* past migrations took. New initiative? Find the success rate for similar efforts. We systematically ignore this and overcommit [1][4][5][13].

2. **Find the bottleneck.** *In practice*: Where does work pile up? Where do people wait? That's your constraint. Improving anything else - adding test automation elsewhere, optimizing a non-blocked stage - doesn't speed delivery [18]. One thing limits throughput.

3. **Use ranges, not single numbers.** *In practice*: "6 months" creates false certainty. "4 to 11 months, most likely 7" is more honest. People's "90% confident" ranges hit the truth only 40-60% of the time [1][5]. Downstream plans inherit your precision - keep it realistic.

4. **Match your approach to the problem.** *In practice*: Repeatable work (deployments, standard migrations) → use benchmarks and process. Novel work (new product, new tech) → run small experiments, don't trust precise forecasts. Chaos (outage, crisis) → stabilize first, analyze later [19]. Precise models for unpredictable problems create false confidence [11].

5. **When worst case can be 10× typical, plan for extremes.** *In practice*: Project overruns, security breaches, innovation bets - the tail dominates. Don't rely on averages. Put most effort in safe options; use a small slice for experiments where the most you can lose is the experiment cost [11].

6. **Reversible = decide fast. Irreversible = invest in getting it right.** *In practice*: Can you undo it? Decide and correct course. Can't undo? Invest in analysis. Stuck? If another week of study costs more than it could possibly save, decide now [2][3]. Set a deadline → [Integrated Reference](04_shared_systems_thinking.md) Principle 6.

7. **Never rely on a single metric.** *In practice*: "Tickets closed" gets gamed. "Deployment frequency" gets gamed. Pair every activity metric with an outcome metric - e.g., deployment frequency *and* change failure rate [12]. Organizations default to measuring what's easy; that becomes the official reality [8][9].

8. **Set exit criteria before you start.** *In practice*: "If we don't see X by date Y, we stop." Write it down before the project begins. Once you've invested, it's hard to kill things - set the bar when you can still walk away [1][13].

9. **Track estimates vs actuals.** *In practice*: Every estimate, record the outcome. Plot predicted vs actual. If you're consistently optimistic, widen your ranges. Without this loop, the same errors repeat forever [3][5].

10. **Measure what would change your decision.** *In practice*: "We can't measure that" usually means "we haven't asked what would change our mind." Measurement is reducing uncertainty, not achieving certainty [3]. If learning X wouldn't change what you'd do, don't measure X.

11. **Queue problems need less work in progress, not more people.** *In practice*: Project late? Usually it's too much WIP - work stacked up, context switching, coordination overhead. Adding people often makes it worse before it gets better [10]. Limit how much you start; finish before starting more [6][7].

12. **Lead with the decision.** *In practice*: Stakeholders want "we recommend X" not "we ran a Monte Carlo." Give ranges, not single numbers. State the critical assumption. Never say "the model says" without "assuming [inputs]" → [Integrated Reference](04_shared_systems_thinking.md) Principle 12.

---

## Distribution, Variance, and Probability: Navigating Complex Systems

These insights help you *understand* how your system behaves - not just decide and forecast, but read the statistical structure so you can navigate it effectively and achieve measurable outcomes. From [Quantitative Reasoning](05_shared_quantitative_reasoning.md) Part I.

### Distribution Shape: Thin Tails vs Fat Tails

**What you'll see**: Some outcomes cluster around a typical value with rare extremes (thin-tailed). Others have a few "normal" cases and a long tail of surprisingly common extremes (fat-tailed).

| Domain | Thin-tailed (bell curve) | Fat-tailed (extremes matter) |
|--------|--------------------------|------------------------------|
| **Examples** | Manufacturing tolerances, mature cycle times, physical measurements | Project overruns, incident impact, security breaches, innovation returns |
| **Average** | Stable, representative | Misleading - pulled by extremes |
| **Planning** | Average is useful | Average understates risk. Plan for extremes |

**What it means for you**: In fat-tailed domains (projects, security, migrations, innovation), the average understates risk. A few extreme events dominate total impact. Don't plan for "typical" - plan for the tail. Use medians and ranges, not means. Invest in resilience, not precise forecasting [05 §2][11].

### Variance: Predictability vs Unpredictability

**What you'll see**: Two teams both average 5-day cycle time. Team A: 4-6 days. Team B: 1-15 days. Same average, completely different predictability.

| Low variance | High variance |
|--------------|---------------|
| Process is controlled. You can plan. | Uncontrolled variables, hidden dependencies, mixed work types |
| Individual data points are informative | Individual data points are mostly noise |
| React to anomalies | Don't react to every blip - look for patterns over time |

**What it means for you**: High variance signals the process needs attention - WIP limits, standardization, dependency reduction. Reducing variance often improves outcomes more than chasing a better average [05 §6][9]. When variance is high, use longer time windows and aggregated views before acting.

### Signal vs Noise: When to React

**What you'll see**: A metric bounces week to week. Is it a real trend or random variation?

| High signal-to-noise | Low signal-to-noise |
|---------------------|----------------------|
| Individual measurements are informative | Individual measurements are mostly noise |
| Investigate each anomaly | Only aggregated patterns or extreme outliers are informative |
| React to changes | Don't react - you'll chase ghosts |

**What it means for you**: Treating noise as signal - investigating every blip, "fixing" random variation - adds complexity without benefit. Plot metrics over time. Is the line drifting or bouncing? Drift = signal. Bounce = noise. React only to signal [8][9]. This is why process behavior charts beat single-threshold alerts.

### Regression to the Mean: When "Improvement" Is Just Luck

**What you'll see**: A team has a terrible quarter → you intervene → next quarter improves. Conclusion: the intervention worked. Often wrong.

**What it means**: Extreme outcomes are partly signal, partly luck. Luck doesn't repeat. After a bad quarter, improvement would often happen with no intervention. The intervention gets credit for a statistical artifact [05 §4][2].

**What to do**: Before attributing change to an intervention, ask: "Would this outcome be expected from regression to the mean alone?" If performance was extreme and the measurement includes noise, the answer is often yes. Genuine effects should be visible beyond what natural bounce-back would produce.

### Base Rates: Start With History, Not Your Gut

**What you'll see**: Each initiative evaluated on its merits. "Our plan is solid." "We've got executive sponsorship." Success rates for similar efforts never consulted.

**What it means**: Base rate neglect - ignoring how often similar things succeed - produces systematic overcommitment. If platform migrations succeed 25% of the time, the burden of proof is on the proponent to explain why this one differs [05 §5][1][4].

**What to do**: Before any significant commitment, ask: *What did similar efforts actually deliver?* Migration? Look up past migrations. New product? Find success rate for similar bets. Alert fired? Check the true-positive rate - if it's 2%, 98% of responses are wasted. Base rate first, then adjust for specific evidence.

### Pareto Concentration: A Few Things Dominate

**What you'll see**: 80% of incidents from 20% of components. 80% of revenue from 20% of customers. 90% of downtime from 5% of failures. Outcomes are extremely uneven.

**What it means**: In complex systems, a small fraction of inputs produces a large fraction of outputs. The "80/20" ratio varies, but the principle holds: concentration is the default [05 §3][10].

**What to do**: Find the right 20%. Focus effort there. Reducing the top 5% of incident types by half often beats eliminating the bottom 50% entirely. Uniform resource allocation across a Pareto-distributed problem guarantees under-investment where it matters and over-investment where it doesn't.

### Probability Traps: Common Missteps

| Trap | What you'll see | What to do |
|------|-----------------|------------|
| **Conjunction fallacy** | "The project will be late because the vendor integration takes longer" feels more likely than "the project will be late" | The specific scenario is a subset of the general one - it can't be more probable. Don't let detailed narratives create false confidence [2]. |
| **Prosecutor's fallacy** | "If the system were failing, we'd see these alerts" → "We're seeing alerts, so the system is failing" | Wrong. "Alert if failing" ≠ "failing if alert." The base rate of benign triggers matters. Check true-positive rate [2]. |
| **Independent risks multiply** | 5 dependencies, each 90% reliable → plan assumes 90% overall | 0.9⁵ ≈ 59%. Joint probability is 41% failure. Each component "looks safe" alone; together they're not [2]. |

---

## The Five Patterns: How to Spot Them

Each pattern from [Integrated Reference](04_shared_systems_thinking.md) has a practical signal. Recognizing it lets you intervene earlier.

### Accumulation Pattern

**How to spot it**: Plot cycle time, lead time, or incident rate over months. Is the line drifting upward - even if it bounces week to week? That's accumulation. *Example*: Cycle time was 5 days, now it's 8. Next quarter it'll be 11 if nothing changes [8]. Calculate the cost: 3 extra days × items per month × cost per day of delay [6].

**Why it compounds**: Each increment makes the next one cost more. Technical debt, process overhead, coupling - they multiply, not add. The trajectory is exponential.

**What to do**: Make the drift visible. Quantify the cost. Budget for reduction alongside delivery. Don't treat it as a one-time cleanup.

### Boundary Pattern

**How to spot it**: When you deploy or change one thing, do failures show up in another? *Example*: Deploying Service A causes incidents in Service B more than 15% of the time. The boundary between them isn't real - failure is leaking across [05 §3](05_shared_quantitative_reasoning.md#3-the-pareto-structure-of-outcomes).

**Why it matters**: A few boundaries matter enormously. Where you draw them determines whether a failure stays contained or cascades.

**What to do**: Define clear interfaces. Accept some duplication over tight coupling. Test: if one component failing disrupts unrelated functions, the boundary is wrong → [Integrated Reference](04_shared_systems_thinking.md).

### Feedback Loop Pattern

**How to spot it**: Do bad periods predict more bad periods? *Example*: A rough sprint is followed by another rough sprint - not random bounce-back. Or: more work in progress → longer cycle times → more work piles up → even longer cycle times [6][7][15]. The system is reinforcing itself.

**Why it persists**: Negative loops feed on themselves. We often credit our interventions when the system would have reverted anyway [05 §4](05_shared_quantitative_reasoning.md#4-regression-to-the-mean).

**What to do**: Break the cycle. For "everyone busy" → WIP limits. For estimation errors → check what similar efforts took before committing. Don't work harder inside the loop.

### Constraint Pattern

**How to spot it**: Where does work pile up? Where do people wait? Map your pipeline - design, build, test, deploy, review. The stage with the longest queue and highest utilization is the constraint [6][16]. *Rule of thumb*: At 90% "busy," wait times are ~9× longer than at 50%. At 95%, ~19× [6][16]. "Keeping everyone busy" creates gridlock.

**Why one dominates**: One bottleneck limits everything. Improving other stages doesn't speed delivery [18].

**What to do**: Improve only the constraint. Spare capacity at non-constraints isn't waste - it's the buffer that absorbs surprise work.

### Visibility Pattern

**How to spot it**: Do most problems surface through your metrics, or through surprises (customer complaint, outage, escalation)? If surprises dominate, you're measuring the wrong things. *Also*: Are estimates ever checked against actuals? If not, you're flying blind. *Also*: Do you only hear about successes? Failures are invisible - survivorship bias [05 §8](05_shared_quantitative_reasoning.md#8-sampling-bias-and-the-data-you-dont-see).

**Why it controls you**: You can only act on what you see. The invisible part - tail risk, unmeasured overhead, tribal knowledge - drives outcomes → [Integrated Reference](04_shared_systems_thinking.md) Principle 7.

**What to do**: List assumptions. Test the critical ones. Pair activity metrics with outcome metrics. Track estimates vs actuals.

---

## Practical Rules of Thumb

| Situation | Rule | What it means for you |
|-----------|------|------------------------|
| **Delivery is slow** | More work in progress = longer wait [7] | If your team has 20 items in flight and completes 5/week, everything takes ~4 weeks. The lever: limit how much you start. Finish before starting more. |
| **"Keep everyone busy"** | At 90% utilized, waits are ~9× longer than at 50% [6][16] | Full task lists feel efficient but create gridlock. Spare capacity absorbs variability. A team at 70% utilized often delivers faster than one at 95%. |
| **Prioritization debates** | High value + short duration beats low value + long duration [6] | Rank by impact per week of delay. Economic ranking beats "who shouted loudest" or "first in, first out." |
| **Comparing options** | Weigh probability × value for each outcome [2] | Helps compare trade-offs. *But*: when downside can be catastrophic (security, migration failure), don't rely on expected value alone - cap the downside. |
| **When to stop analyzing** | If learning more wouldn't change your decision, stop [2][3] | Another week of analysis has zero value if you'd choose the same option either way. If delay cost exceeds what you could learn, decide now. |
| **Portfolio planning** | Independent risks multiply | 5 projects, each 70% likely on time → 17% chance *all* on time [1]. Plans that assume every project delivers are usually wrong. Build in slack or phase commitments. |
| **High variance in outcomes** | Variance often matters more than the average [9] | Same average cycle time, but 1-15 days vs 4-6 days? The high-variance case is unpredictable. Reduce variance (WIP limits, standardization) before optimizing the mean. |
| **Fat-tailed domain** | Extremes dominate; average understates risk [11] | Projects, security, migrations, innovation: plan for the tail. Use medians and ranges. Invest in resilience (blast radius limits, optionality) not precise forecasts. |
| **Metric bouncing week to week** | Signal vs noise [8][9] | Plot over time. Drift = real trend, act. Bounce = noise, don't react. Reacting to every blip increases variation and degrades the system. |

---

## Early Warning Signs

If you see three or more of these, something systematic is wrong. Each is fixable if you catch it early.

| # | What you'll see | What it means | What to do |
|---|-----------------|---------------|------------|
| 1 | Plans keep slipping the same way - always late, never early | Your estimates are systematically optimistic. You're not learning from past misses | Track estimates vs actuals. Widen your ranges. Check what similar efforts took |
| 2 | Major decisions made without looking up what similar efforts delivered | You're relying on your gut instead of history. Planning fallacy in action | Before committing: what did similar migrations/projects/initiatives actually achieve? [4][13] |
| 3 | Plans show single dates: "Delivery: June 15" | False precision. Downstream plans (staffing, dependencies) inherit it. One slip cascades | Use ranges: "June to August, most likely July." Match precision to what you know |
| 4 | Everyone has a full task list; nothing seems to move | Utilization too high. Queue explosion. At 90% busy, waits are ~9× longer than at 50% [6][16] | Limit WIP. Accept that spare capacity is necessary. Stop starting, start finishing |
| 5 | Work in progress growing faster than completions | Cycle time will spike. More items in flight = everything takes longer [7] | WIP limits. Finish before starting. Find why completions are slowing |
| 6 | Dashboards show activity (tickets closed, deployments) but not outcomes (value delivered, problems solved) | You're measuring what's easy. Teams will optimize the visible metric, not the outcome [8][9] | Pair every activity metric with an outcome metric. Ask: "What does this proxy for?" |
| 7 | Incident rate is steady (e.g., 3-5/month) but each postmortem finds a "root cause" and "fixes" it | The rate is stable - the system is working as designed. "Fixing" individual incidents is tampering [9] | If the rate is stable, change the system. Don't investigate every incident as if it's special |
| 8 | No one tracks whether estimates were right | No feedback loop. Same errors repeat. Your organization doesn't learn [3][5] | Record every estimate. Plot predicted vs actual. Use it to widen ranges |
| 9 | Sophisticated model (Monte Carlo, detailed forecast) for something novel or unpredictable | Wrong tool. Precision creates false confidence when cause-effect is unstable [11] | For novel/unpredictable: run small experiments. Use simple rules. Don't trust the model |
| 10 | Decision "under review" for weeks; the cost of delay exceeds the difference between options | Analysis paralysis. More study won't help [2][3] | Set a deadline. If delay cost > learning value, decide now |
| 11 | Portfolio plan assumes all 5 (or 10) projects deliver on time | 5 projects × 70% each = 17% all on time. The plan is wrong [1] | Phase commitments. Build in slack. Don't assume everything works |
| 12 | "We can't measure that" used to skip quantification | Measurement is reducing uncertainty, not achieving certainty [3]. The barrier is usually belief | Ask: what would we need to know to change our decision? Then find a way to learn it - even roughly |
| 13 | Outcomes wildly variable (e.g., cycle time 1-15 days) but plans assume "average" | High variance + average-based planning = chronic surprises. The average is misleading when variance is high [9] | Reduce variance first (WIP, standardization). Use ranges. Plan for the spread, not the mean |
| 14 | "We fixed it" after bad quarter → next quarter improved | Regression to the mean. Extreme outcomes bounce back; intervention may have gotten credit for luck [2][05 §4](05_shared_quantitative_reasoning.md#4-regression-to-the-mean) | Ask: would improvement be expected from regression alone? Genuine effects persist beyond natural bounce |

---

## Never Rules & First-Step Rules

### Never Rules

| # | Rule | In practice |
|---|------|-------------|
| 1 | Never estimate without checking what similar efforts actually took | Migration? Look up past migrations. New product? Find success rate for similar bets. Your view of your project is biased [1][4] |
| 2 | Never use a single number for planning | "6 months" creates false certainty. Use "4 to 11 months, most likely 7." Downstream plans inherit your precision [1][5] |
| 3 | Never use precise models for unpredictable problems | Monte Carlo for a novel market entry? Wrong tool. Run experiments instead. Precision creates false confidence [8][11] |
| 4 | Never target a single metric | "Tickets closed" → people close trivial tickets. Pair with outcome: "tickets closed *and* customer issues resolved" [8][9] |
| 5 | Never assume "typical" for projects, security, innovation | Worst cases dominate. Project overruns, breaches, failed bets - plan for extremes, not averages [11] |
| 6 | Never skip tracking estimates vs actuals | Without this, you repeat the same errors. Plot predicted vs actual. Widen ranges when you're consistently wrong [3][5] |
| 7 | Never add people to fix a late project without checking if it's a queue problem | Usually it's too much WIP. Adding people adds WIP (onboarding, coordination). Often makes it worse [10] |
| 8 | Never treat every incident as special when the rate is stable | 3-5 incidents/month, steady? The system is designed to produce that. Fix the system, not each incident [8][9] |
| 9 | Never treat noise as signal | Metric bounces week to week? Don't investigate every blip. Plot over time. React to drift, not bounce [8][9] |
| 10 | Never plan for the average when variance is high | High variance = unpredictable. Average is misleading. Use ranges, reduce variance, plan for the spread [9] |

### First-Step Rules

| Situation | First move | Example |
|-----------|------------|---------|
| Unfamiliar problem | What actually happened in similar situations? [4][13] | New platform? Find what similar adoptions took. Don't trust the vendor's timeline |
| Project is late | Is it capacity or queue? What did similar projects take? [4][7] | Migration 4 months in, 30% done? Check: (a) capacity vs queue - are people idle or is work stacked? (b) What did similar migrations actually take at this point? |
| Recurring failures | Is the failure rate stable or changing? [8] | Plot incidents per month. Stable line = systemic. Changing = something specific happened. Respond accordingly |
| High-uncertainty investment | Run the smallest experiment that tests the critical assumption [13] | $5M migration? Run $50K proof of concept first. Test the thing that would kill the project |
| Conflicting priorities | Rank by impact per week of delay [6] | Economic ranking beats politics. High value + short = first. Low value + long = last |
| Choosing between options | Which assumptions drive the outcome? Test those first [2][3] | Build vs buy? The decision probably turns on 1-2 assumptions (e.g., team velocity, integration complexity). Test those |
| Gradual degradation | Plot the trend. Quantify the cost of delay [6] | Cycle time creeping up? Plot it. "7 extra days × 10 items/month × $500/day = $35K/month." Makes the case for fixing it |
| "We can't measure that" | What would we need to know to change our decision? [3] | If the answer is "nothing - we'd do the same either way," skip it. If something would change your mind, find a way to learn it - even roughly |
| Estimates wrong same direction | Track estimates vs actuals. Use similar-past-effort data [3][4][5] | Consistently optimistic? Widen ranges. Start with "what did similar efforts take?" before your gut |
| Metric changed - real trend or noise? | Plot over time. Is it drifting or bouncing? [8][9] | Drift = signal, investigate. Bounce = noise, don't react. Single data points in high-variance systems are mostly noise |

---

## Problem Type → Approach

| Problem type | What it means | What to do | Avoid |
|--------------|---------------|------------|-------|
| **Predictable** | We've done this before. Cause and effect known. | Use benchmarks. Standard process. Historical data. | Over-analyzing |
| **Discoverable** | Experts can figure it out. Cause-effect exists but we need to find it. | Analyze. Model. Get expert input. Test assumptions. | Skipping analysis or refusing to commit |
| **Unpredictable** | New tech, new market, emergent behavior. Only clear in hindsight. | Run small experiments. Learn from evidence. Use simple rules. [19][14] | Precise forecasts. Complex models. Optimization. |
| **Chaotic** | No discernible pattern. Crisis, outage, major disruption. | Stabilize first. Then analyze. | Any quantitative approach until you've stabilized |

**When worst case can be 10× typical** (projects, security, innovation): Don't rely on averages. Put 80-90% in safe options; use 10-20% for experiments where the most you can lose is the experiment cost [11].

---

## Decision Frameworks

### 5-Step Decision Check

When facing a significant decision, run through these questions. Extends the Decision Engine in [Integrated Reference](04_shared_systems_thinking.md).

```
  1. Phase?     → Understand: What did similar efforts deliver? Analyze: What drives the outcome? Where's the bottleneck? Decide: Weigh trade-offs. What's the downside? When to stop analyzing? Execute: Plot metrics over time. Limit WIP. Adapt: Compare predictions to outcomes. Update expectations.
  2. Problem type? → Predictable / Discoverable / Unpredictable / Chaotic [19] → match your approach (see table above)
  3. Reversible? → Yes: decide fast, correct course. No: invest in getting it right. Uncertain: run a small experiment first [13]
  4. Constraint? → Where does work pile up? Improve only that [18]
  5. Assumptions? → List them. Which would change the answer if wrong? Test those first. Know when more info isn't worth the wait [2][3]
```

### Staged Investment (High Uncertainty, Large Commitment)

For platform migrations, major builds, new market entries - when the commitment is big and the uncertainty is high.

```
  Frame   → What's the success rate for similar efforts? What must be true for this to work? What's the downside if we're wrong? [1][4][11]
  Option  → Run the cheapest test of the most critical assumption. Set exit criteria before you start: "If X doesn't happen by Y, we stop." [13]
  Evaluate→ Did the evidence clearly support or contradict? Update your view. Gate check.
  Commit  → Met criteria: fund next step. Failed: execute the exit. Ambiguous: design a clearer test - don't default to continuing
  Learn   → Compare predictions to outcomes. Update your expectations for next time. Document the chain [3][5]
```

**Gate** [1][4]: If similar efforts succeed less than 30% of the time and you can't point to a specific, verifiable reason this one differs → default = don't proceed.

---

## Ripple Effect Maps

How quantitative failures cascade. Complement the structural cascades in [Integrated Reference](04_shared_systems_thinking.md). **What you'll see** → **How it unfolds** → **Where to break it**.

### The Estimation Cascade

**What you'll see**: A plan with a single date. "Migration complete: June 15." Downstream plans (staffing, dependencies, budgets) built on it.

**How it unfolds**:
```
  Single-date estimate accepted
    → downstream plans assume it's right
      → portfolio assumes all 5 projects deliver (but 5×70% = 17% chance)
        → first project slips → cascading replans → everyone's fighting for resources
          → quality shortcuts to "recover" → more incidents → more overhead → further delays
```

**Why it happens**: People's "90% confident" ranges hit the truth only 40-60% [1][5]. Errors compound across the portfolio.

**Where to break it**: At the estimation step. Use ranges. Check what similar efforts actually took [3][4][5]. Track estimates vs actuals.

### The Utilization Cascade

**What you'll see**: "Keep everyone busy" - full task lists, no idle time. Feels efficient. Delivery is slowing.

**How it unfolds**:
```
  "Everyone busy" policy
    → utilization rises above 85%
      → wait times explode (at 90% busy, waits are ~9× longer than at 50%) [6][16]
        → cycle time spikes → more work piles up before previous work finishes
          → pressure to "do something" → add people → onboarding adds more WIP → worse
            → delivery collapses
```

**Why it happens**: The relationship between utilization and wait time is not linear. High variability (changing requirements, unplanned work) makes it worse [6][16].

**Where to break it**: WIP limits. Accept spare capacity. Stop starting, start finishing [6][7].

### The Measurement Dysfunction Cascade

**What you'll see**: Dashboards full of activity (tickets closed, deployments). Outcomes (value delivered, problems solved) not measured.

**How it unfolds**:
```
  Activity metrics adopted (easy to collect)
    → teams optimize for what's measured
      → dashboard looks healthy while real outcomes degrade
        → gap widens invisibly → crisis surfaces (customer impact, outage)
          → more metrics added, more governance → overhead grows → performance worsens
```

**Why it happens**: When you optimize a proxy, you diverge from the real goal [8][9]. Single metrics get gamed.

**Where to break it**: Pair every activity metric with an outcome metric from the start [12]. "Tickets closed" + "customer issues resolved."

---

## Anti-Patterns & Countermeasures

| What you'll see | Why it happens | What to do instead |
|-----------------|----------------|---------------------|
| **Plans with fake precision** ("$2,347,891" when range is $1M-$5M) | Spreadsheets and templates reward specificity [3] | Use ranges. "Between $1.5M and $4M, most likely $2.5M." Match precision to what you know |
| **Dashboards of activity, no outcomes** | Activity metrics are easy; outcome metrics require defining "value" | Ask: "What outcome does this proxy for?" One outcome metric per capability. If you can't answer, the metric is decorative |
| **Metrics get gamed** (deployments increase via trivial splits) | Targets create incentive to optimize the number, not the system [8][9] | Pair metrics so gaming one hurts the other (deployments + change failure rate). Use trends over time, not single thresholds |
| **"The model says 73%"** (treated as fact) | Numbers from models feel objective even when inputs were guesses [1] | "This assumes X. If X is wrong, the result changes to Y." When reality contradicts the model, question the model first |
| **Risk matrix says "Low likelihood, High impact" - deprioritized** | Ordinal scales feel manageable; extreme outcomes feel implausible [11] | When worst case can be 10×+ typical, plan for extremes. Invest in resilience, not forecasting |
| **Estimates never checked** (2-week estimates routinely take 5 weeks, repeatedly) | No feedback loop. No one compares plan to actual [3][5] | Track every estimate. Plot predicted vs actual. Use it to widen ranges. Estimation without feedback is ritual |
| **Decision "under review" for weeks** | Delay feels safe; being wrong feels dangerous [2][3] | If another week of analysis costs more than it could save, decide now. Set a deadline → [Integrated Reference](04_shared_systems_thinking.md) Principle 6 |

**The deepest failure**: No feedback loop between predictions and outcomes. Without it, every other anti-pattern is invisible and self-reinforcing.

---

## Communication

How to frame quantitative insights for different audiences. Lead with the decision, not the methodology.

| Audience | What they need | How to say it |
|----------|----------------|---------------|
| **Executive** | Decision, impact, confidence | "We recommend X. Benefit: $Y/year. Confidence 70% based on similar efforts [4]. Key risk: [assumption]" |
| **Steering** | Options, trade-offs, what the decision turns on | "Option A has higher expected value but wider variance. The decision turns on [assumption]. We'd test it by [experiment]" |
| **Delivery** | Clear targets, when to escalate | "WIP limit: 8. If cycle time exceeds 10 days, investigate before starting new work" [8] |
| **Finance** | Ranges, contingency, basis | "Budget: $1.2M-$2.1M (90% range). Contingency: $400K based on typical overruns for similar efforts [4]" |

**Rules**: Ranges, not single numbers. State the critical assumption. Never say "the model says" without "assuming [inputs]." Lead with the decision → [Integrated Reference](04_shared_systems_thinking.md) Principle 12.

---

## The Utilization Trap: Quick Reference

**What it means for you**: The busier your team is, the longer everything takes - and the relationship is not linear. "Keeping everyone busy" maximizes utilization at the expense of delivery speed.

| How busy? | Wait times vs. "normal" | What you'll see |
|:---------:|:----------------------:|-----------------|
| 50% | ~1× | Waits roughly equal work time |
| 70% | ~2× | Moderate queues |
| 80% | ~4× | Substantial queues |
| 90% | ~9× | Queues dominate - everything feels stuck |
| 95% | ~19× | Near gridlock |

**In practice**: A team at 70% utilized (some spare capacity) often delivers faster than one at 95% (everyone has a full task list). Spare capacity isn't waste - it's the buffer that absorbs surprise work, context switches, and variability [6][16]. High-variability environments (changing requirements, unplanned work, cross-team dependencies) hit gridlock at lower utilization than you'd expect [05 §9](05_shared_quantitative_reasoning.md#9-nonlinear-dynamics-and-phase-transitions).

---

## References

1. Kahneman, D. (2011). *Thinking, Fast and Slow*. Farrar, Straus and Giroux. ISBN: 978-0-374-27563-1
2. Raiffa, H. (1968). *Decision Analysis: Introductory Lectures on Choices under Uncertainty*. Addison-Wesley.
3. Hubbard, D.W. (2014). *How to Measure Anything: Finding the Value of "Intangibles" in Business* (3rd ed.). Wiley. ISBN: 978-1-118-53927-9
4. Flyvbjerg, B. (2006). "From Nobel Prize to Project Management: Getting Risks Right." *Project Management Journal*, 37(3), 5-15.
5. Tetlock, P.E. & Gardner, D. (2015). *Superforecasting: The Art and Science of Prediction*. Crown. ISBN: 978-0-804-13667-6
6. Reinertsen, D.G. (2009). *The Principles of Product Development Flow: Second Generation Lean Product Development*. Celeritas Publishing. ISBN: 978-1-935401-00-1
7. Little, J.D.C. (1961). "A Proof for the Queuing Formula: L = λW." *Operations Research*, 9(3), 383-387.
8. Wheeler, D.J. (1993). *Understanding Variation: The Key to Managing Chaos*. SPC Press. ISBN: 978-0-945320-35-8
9. Deming, W.E. (1986). *Out of the Crisis*. MIT Press. ISBN: 978-0-262-54115-2
10. Brooks, F.P. (1995). *The Mythical Man-Month: Essays on Software Development* (Anniversary ed.). Addison-Wesley. ISBN: 978-0-201-83595-3
11. Taleb, N.N. (2007). *The Black Swan: The Impact of the Highly Improbable*. Random House.
12. Forsgren, N., Humble, J. & Kim, G. (2018). *Accelerate: The Science of Lean Software and DevOps*. IT Revolution Press. ISBN: 978-1-942788-33-1
13. McGrath, R.G. & MacMillan, I.C. (1995). "Discovery-Driven Planning." *Harvard Business Review*, 73(4), 44-54.
14. Gigerenzer, G. (2014). *Risk Savvy: How to Make Good Decisions*. Penguin. ISBN: 978-0-241-95461-4
15. Meadows, D.H. (2008). *Thinking in Systems: A Primer*. Chelsea Green Publishing. ISBN: 978-1-603-58055-7
16. Hopp, W.J. & Spearman, M.L. (2008). *Factory Physics* (3rd ed.). Waveland Press. ISBN: 978-1-577-66739-1
17. Flyvbjerg, B. (2021). "Top Ten Behavioral Biases in Project Management: An Overview." *Project Management Journal*, 52(6), 531-546.
18. Goldratt, E.M. (1984). *The Goal: A Process of Ongoing Improvement*. North River Press. ISBN: 978-0-884-27061-4
19. Snowden, D.J. & Boone, M.E. (2007). "A Leader's Framework for Decision Making." *Harvard Business Review*, 85(11), 68-76.

---

> **Foundation**: [Integrated Reference](04_shared_systems_thinking.md) — lifecycle, patterns, principles
>
> **Deeper understanding**: [Quantitative Reasoning](05_shared_quantitative_reasoning.md) — statistical foundations, methods, applied scenarios
