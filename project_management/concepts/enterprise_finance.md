# Enterprise Financial Standards & Practices

**Scope**: Accounting standards, financial reporting frameworks, fiscal year management, capital asset accounting, year-end processes, audit, and internal controls relevant to enterprise IT and project management — with emphasis on accountability-driven organizations operating under PSAS, IFRS, or equivalent frameworks.

**Purpose**: Use this as a reference for the financial and accounting context that governs enterprise IT investments. For business case development, investment appraisal, and cost estimation, see [Business & Financial Practices](business_financial.md). For cloud financial management, see [FinOps](finops.md). For enterprise risk including financial risk, see [Risk Management](risk_management.md).

## Table of Contents

- [1. Accounting Standards Landscape](#1-accounting-standards-landscape)
- [2. PSAS & IFRS in Practice](#2-psas--ifrs-in-practice)
- [3. Accrual Accounting](#3-accrual-accounting)
- [4. Capital Assets & Capitalization](#4-capital-assets--capitalization)
- [5. Revenue Recognition & Transfer Payments](#5-revenue-recognition--transfer-payments)
- [6. Fiscal Year & Budget Cycle](#6-fiscal-year--budget-cycle)
- [7. In-Year Financial Management](#7-in-year-financial-management)
- [8. Year-End Processes](#8-year-end-processes)
- [9. Financial Audit](#9-financial-audit)
- [10. Internal Controls](#10-internal-controls)
- [11. Financial Reporting & Accountability](#11-financial-reporting--accountability)
- [12. Implications for IT & Project Management](#12-implications-for-it--project-management)

---

## 1. Accounting Standards Landscape

### Standards Framework

| Standard | Issuing Body | Applies To |
|----------|-------------|-----------|
| **PSAS** (Public Sector Accounting Standards) | PSAB (Public Sector Accounting Board) | Government ministries, agencies, broader public sector entities |
| **IFRS** (International Financial Reporting Standards) | IASB | Government business enterprises (GBEs), publicly accountable entities, publicly traded companies |
| **GAAP** | Umbrella term | PSAS for government entities, IFRS for publicly accountable entities, ASPE for private enterprises |
| **ASPE** | AcSB (Accounting Standards Board) | Private enterprises not publicly accountable |
| **IPSAS** | IPSASB | International public sector reference; influential but not directly binding domestically |

### Which Standard Applies

```
  Government or government component?
       │
       ├── Yes ──▶ PSAS
       │              └── Exception: Government Business Enterprises ──▶ IFRS
       │
       └── No
              ├── Publicly accountable? ──▶ IFRS
              └── Private enterprise? ──▶ ASPE (or IFRS by choice)
```

PSAB sets standards for the public sector through the CPA Canada Public Sector Accounting Handbook, developing guidance through exposure drafts and public consultation. PSAB also considers alignment with IPSAS and IFRS where appropriate for the public sector context.

---

## 2. PSAS & IFRS in Practice

### Key PSAS Sections for IT/PM

| Section | Topic | Relevance |
|---------|-------|-----------|
| **PS 3150** | Tangible capital assets | How IT infrastructure and software are capitalized and amortized |
| **PS 3210** | Assets | When an IT investment becomes an asset on the balance sheet |
| **PS 3390** | Contractual obligations | Disclosure of multi-year contracts (cloud, licensing, outsourcing) |
| **PS 3400** | Revenue | Revenue recognition; relevant for fee-for-service programs |
| **PS 3410** | Government transfers | Accounting for grants, transfer payments, intergovernmental funding |
| **PS 3450** | Financial instruments | Complex funding arrangements involving financial instruments |

### PSAS vs IFRS: Key Differences

| Dimension | PSAS | IFRS |
|-----------|------|------|
| **Primary objective** | Accountability and stewardship | Decision-useful information for investors |
| **User focus** | Legislature, public, oversight bodies | Investors, creditors, capital markets |
| **Revenue** | Authority-based; non-exchange transactions common | Performance obligations and customer contracts |
| **Assets** | Tangible capital assets; limited intangibles | Broad recognition including intangibles, fair-value measurement |
| **Budget reporting** | Budget-to-actual comparison required | No budget comparison required |
| **Key metric** | Net debt (financial assets minus liabilities) | Equity and earnings |

### Shared Principles

Both frameworks require full accrual accounting, substance over form, consistent application for comparability, and reliable measurement. The differences lie in audience, purpose, and the treatment of non-exchange transactions that are common in accountability-driven environments.

---

## 3. Accrual Accounting

### Cash vs Accrual

| Dimension | Cash Basis | Accrual Basis |
|-----------|-----------|---------------|
| **Revenue** | Recorded when cash received | Recorded when earned or right to receive is established |
| **Expenses** | Recorded when cash paid | Recorded when goods/services received or obligation incurred |
| **Assets / Liabilities** | Not systematically tracked | Recognized on the statement of financial position |

Entities operating under PSAS or IFRS use full accrual accounting, though budgets in accountability-driven environments are often managed on a near-cash or modified accrual basis. PMs must understand both perspectives.

### Accrual Implications for Projects

| Situation | Treatment | PM Implication |
|-----------|-----------|---------------|
| Invoice received, not yet paid | Expense recognized (accrued liability) | Cost hits project financials even if payment crosses fiscal year |
| Prepayment for future services | Asset (prepaid) until consumed | Upfront cloud or license payments don't hit expense immediately |
| Multi-year contract | Expense as services consumed each period | Annual costs allocated across fiscal years |
| Capital asset under construction | Work-in-progress (asset); not expensed until in service | Large IT builds capitalize during development |
| Year-end accruals | Goods received but not invoiced must be estimated | PM must report accruals to finance at year-end |

---

## 4. Capital Assets & Capitalization

### Capitalization Criteria

An expenditure is capitalized (recorded as an asset) rather than expensed when it provides future economic benefit beyond the current fiscal year, can be reliably measured, is controlled by the entity, results from a past transaction, and exceeds the organization's capitalization threshold (commonly $5K-$50K).

### IT-Specific Capitalization

| Expenditure | Capitalize? | Rationale |
|-------------|-------------|-----------|
| Hardware (servers, network, storage) | Yes | Physical assets with multi-year useful life |
| Custom software — research/feasibility phase | No | Outcome too uncertain |
| Custom software — development phase | Yes | Technical feasibility established; directly attributable costs |
| Custom software — post-implementation | No | Maintenance is a period cost |
| Commercial licenses (perpetual) | Yes | Right-to-use asset with multi-year benefit |
| SaaS / cloud subscriptions | No | No asset controlled; service consumed as delivered |
| Data migration | Depends | Capitalize if integral to putting the asset into service |
| Training and change management | No | Benefits the organization, not the asset |

### Amortization

Amortization systematically allocates an asset's cost over its estimated useful life. Straight-line is most common (equal annual amounts). IT assets typically have 5-10 year useful lives and $0 residual value. Impairment write-downs are required when service potential has permanently declined.

### Capital vs Operating Impact

```
  Capital: $1M asset ──▶ $200K/yr amortization over 5 years
  Operating: $1M expense ──▶ hits annual surplus/deficit immediately

  Same total cost. Different timing. Different financial statement impact.
  Different budget stream. Different approval authority.
```

---

## 5. Revenue Recognition & Transfer Payments

### Revenue Types

| Type | Recognition | Examples |
|------|-------------|---------|
| **Taxation** | When taxable event occurs and amount can be estimated | Income, property, sales tax |
| **Government transfers** | When authorized and eligibility criteria met | Grants, conditional funding, program transfers |
| **User fees** | When service is provided | Licensing fees, permits, service charges |
| **Investment income** | As earned | Interest, dividends |

### Transfer Payments (PS 3410)

Transfer payments — grants, subsidies, and contributions — are a defining feature of accountability-driven finance. Recognition depends on authorization, eligibility criteria, stipulations, and recipient accountability requirements.

**IT/PM relevance**: Technology projects funded through transfers often carry conditions (milestones, reporting, repayment clauses). Systems that administer transfer payments must support authorization, eligibility, payment processing, and audit trails. Multi-year transfer agreements create contractual obligations disclosed under PS 3390.

---

## 6. Fiscal Year & Budget Cycle

### Fiscal Year Structure

Many accountability-driven organizations operate on an April 1 to March 31 fiscal year, with budget development beginning 6-12 months before the year starts.

```
  Apr ── May ── Jun ── Jul ── Aug ── Sep ── Oct ── Nov ── Dec ── Jan ── Feb ── Mar
  │                                          │                            │       │
  Fiscal year                            Budget development          Year-end   Fiscal
  begins (Q1)           (Q2)             begins (Q3)                crunch     year ends
                                                                       (Q4)
```

### Budget Development

| Phase | Activities |
|-------|-----------|
| **Planning & guidance** | Central agency issues budget call, targets, and planning assumptions |
| **Departmental submissions** | Business cases, cost estimates, FTE plans, capital plans prepared |
| **Central review** | Treasury Board or equivalent reviews, challenges, and prioritizes |
| **Approval** | Budget approved through executive governance; Estimates tabled for legislative authority |
| **Supply** | Legislature votes spending authority (Appropriation Act); interim supply if needed at year start |

### Budget Authorities

| Authority | Meaning |
|-----------|---------|
| **Voted appropriation** | Legislature-approved spending for a specific purpose; cannot be exceeded without supplementary estimates |
| **Statutory appropriation** | Spending authorized directly by legislation; no annual vote needed |
| **Allotment** | Internal allocation of appropriation to programs; administrative control |
| **Commitment authority** | Approval to enter contracts; may require separate approval |
| **Transfer authority** | Moving funds between votes/programs; usually requires central approval |

---

## 7. In-Year Financial Management

### Ongoing Activities

| Activity | Purpose | Frequency |
|----------|---------|-----------|
| **Expenditure tracking** | Monitor actuals against budget and forecast | Monthly |
| **Variance analysis** | Explain deviations; identify corrective actions | Monthly |
| **Forecast updates** | Revise year-end projection | Quarterly; monthly in Q3-Q4 |
| **Commitment management** | Track outstanding POs, contracts, anticipated obligations | Continuous |
| **Reallocation requests** | Propose budget transfers as priorities shift | As needed; per delegated authority |

### Variance Categories

| Type | Description |
|------|-------------|
| **Timing** | Spending shifted between quarters; no year-end impact |
| **Rate** | Unit costs differ from budget (salary rates, vendor pricing) |
| **Volume** | More or fewer units than planned (transactions, FTEs, licenses) |
| **Scope** | New requirements not in original budget |
| **Lapse** | Projected underspend; funds may be reallocated or returned |
| **Pressure** | Projected overspend; requires management action |

Year-end spending spikes are a well-documented pattern in environments with lapsing budgets. They result from "use it or lose it" rules and carry value-for-money and governance risks.

---

## 8. Year-End Processes

### Timeline

```
  Feb              Mar 31            Apr-May           May-Jun          Fall
  ───              ──────            ───────           ───────          ────
  Pre-close        Fiscal            Soft close        Hard close       Audited
  preparation      year-end          & accruals        & audit          statements
                                     finalized         fieldwork        published
```

### PM Responsibilities at Year-End

| Task | Detail |
|------|--------|
| **Report accruals** | Identify work received but not invoiced; provide amounts and documentation to finance |
| **Confirm capital vs operating** | Clarify which expenditures capitalize vs expense |
| **Final forecast** | Provide year-end projection for project spending |
| **Close commitments** | Cancel or adjust POs that will not be used |
| **Carry-forward requests** | Submit with justification if project funds must cross the fiscal year boundary |
| **Audit support** | Respond to inquiries about project transactions, contracts, and asset status |

---

## 9. Financial Audit

### Audit Types

| Type | Focus | Conducted By |
|------|-------|-------------|
| **Financial statement audit** | Fair presentation per applicable standards (PSAS/IFRS) | Auditor General or external auditor |
| **Compliance audit** | Spending in accordance with authorities and policies | Internal audit, Auditor General |
| **Value-for-money (performance) audit** | Economy, efficiency, and effectiveness of resource use | Auditor General |
| **IT audit** | IT controls, security, and governance adequacy | Internal audit, Auditor General |

### Audit Process

```
  Planning ──▶ Risk Assessment ──▶ Fieldwork ──▶ Reporting
     │               │                 │              │
  Understand       Identify          Test           Issue
  entity &         material          controls &     opinion &
  environment      risk areas        substantive    findings
```

### Audit Opinions

| Opinion | Meaning |
|---------|---------|
| **Unqualified (clean)** | Fairly presented; no material misstatements |
| **Qualified** | Fairly presented except for a specific described matter |
| **Adverse** | Materially misstated; does not present fairly |
| **Disclaimer** | Unable to form an opinion (insufficient evidence) |

### IT-Specific Audit Concerns

| Area | Focus |
|------|-------|
| **IT general controls (ITGCs)** | Access controls, change management, operations — auditors rely on ITGCs to trust system-generated financial data |
| **Application controls** | Input validation, processing controls, output controls in financial systems |
| **Vendor controls** | Third-party service provider assurance (SOC reports, access reviews) |
| **Data integrity** | Accuracy, completeness, and validity of data in financial reporting |

---

## 10. Internal Controls

### COSO Framework

| Component | Application |
|-----------|-------------|
| **Control environment** | Tone at the top; ethics codes, conflict of interest policies, management accountability |
| **Risk assessment** | Enterprise risk management, fraud risk assessment |
| **Control activities** | Segregation of duties, approvals, reconciliations, access controls |
| **Information & communication** | Financial reporting systems, management reports, policy communication |
| **Monitoring** | Internal audit, management reviews, control self-assessments |

### Key Financial Controls

| Control | Purpose |
|---------|---------|
| **Segregation of duties** | No single person initiates, approves, records, and reconciles a transaction |
| **Delegated financial authority** | Spending authority tied to positions with defined thresholds; exceeding requires escalation |
| **Expenditure verification (s.34 equivalent)** | Certification that goods/services were received and the price is reasonable before payment |
| **Payment authority (s.33 equivalent)** | Certification that funds are available and the charge is lawful against the appropriation |
| **Commitment control** | Funds encumbered at PO/contract; prevents over-commitment |
| **Reconciliation** | Periodic comparison of project records to financial system to identify discrepancies |

---

## 11. Financial Reporting & Accountability

### Financial Statements

Audited financial statements (public accounts) are the primary accountability document. Under PSAS, they include a statement of financial position, statement of operations, statement of change in net debt, statement of cash flow, and notes.

**Key indicators**: Net debt (financial assets minus liabilities) and annual surplus/deficit (revenue minus expenses) are the central measures of fiscal position.

### Budget-to-Actual Reporting

PSAS requires budget-to-actual comparison as a financial statement or note — a distinguishing feature of accountability-driven reporting. This includes original budget, revised budget (after supplementary estimates and transfers), actual results, and explained variances.

### Accountability Reports

| Report | Purpose | Frequency |
|--------|---------|-----------|
| **Public accounts** | Audited financial statements | Annual |
| **Estimates** | Planned expenditures seeking legislative spending authority | Annual |
| **Supplementary estimates** | Additional in-year spending authority | As needed |
| **Annual / departmental report** | Performance, results, financial outcomes | Annual |
| **Internal financial reports** | Variance analysis, forecasts, financial risk | Monthly/quarterly |

---

## 12. Implications for IT & Project Management

### Financial Literacy for PMs

| Concept | Why It Matters |
|---------|---------------|
| **Capital vs operating** | Determines budget stream, reporting treatment, and approval authority |
| **Fiscal year boundaries** | Unspent funds may lapse; multi-year projects must plan funding across years |
| **Accrual vs cash** | PM reports both: accruals for financial statements, cash for budget management |
| **Appropriation limits** | Exceeding voted authority is a compliance failure, not just a management issue |
| **Year-end cut-off** | Transactions must be recorded in the correct year; PM input on accruals is essential |
| **Audit readiness** | Documentation must support auditor inquiries about project transactions |

### Common Financial Mistakes

| Mistake | Consequence |
|---------|-----------|
| Confusing commitment with expenditure | Budget appears consumed at PO, but expense is recognized when goods/services arrive |
| Ignoring capital/operating split | Misrepresents financial position; may breach accounting standards |
| Missing accrual deadlines | Omissions from financial statements; audit findings |
| Assuming funds carry forward | Most budgets lapse at year-end; carry-forward requires approval |
| Committing without delegation | Control failure; potential personal liability |
| Coding to wrong fund/account | Budget distortion and compliance risk |

### Financial Calendar

| Period | PM Action |
|--------|----------|
| **Sep-Oct** | Budget submissions for next fiscal year; cost estimates for continuing and new projects |
| **Dec-Jan** | Finalize forecasts; support business case submissions for central review |
| **Mar** | Report accruals; close unused commitments; carry-forward requests; year-end cut-off |
| **Apr** | Confirm new-year funding; establish POs under new appropriation |
| **Ongoing** | Monthly expenditure tracking; quarterly variance analysis; audit-ready documentation |

---

> **Note**: For business case development, investment appraisal, and institutional financial context, see [Business & Financial Practices](business_financial.md). For cloud financial management, see [FinOps](finops.md). For enterprise risk including financial risk, see [Risk Management](risk_management.md). For project-level budget and cost management, see [Project Management](project_management.md).
