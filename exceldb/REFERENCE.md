# Excel Knowledge Database Reference

**Source**: `TERMINOLOGY_REFERENCE.csv` (~957 terms) · **Guide**: [IMPLEMENTATION.md](IMPLEMENTATION.md) · **Formulas**: [EXCEL_FUNCTIONS.md](EXCEL_FUNCTIONS.md)

---

## Architecture

```
TERMINOLOGY_KNOWLEDGE.xlsx
├── [MASTER]     ← Hidden, locked source (table: TermMaster)
├── [INDEX]      ← Dashboard with slicers + filtered table
└── [L1 SHEETS]  ← DATA, SOFTWARE, CLOUD, DEVOPS, SECURITY, AI_ML, PM, PROGRAMMING, BUSINESS, JUSTICE
```

---

## Columns

| Col | Name | Width | Description |
|-----|------|-------|-------------|
| A | Term | 30 | Concept name (acronym in parentheses) |
| B | Definition | 80 | Concise description; no trailing period |
| C | L1_Category | 12 | UPPERCASE top-level |
| D | L2_Subcategory | 15 | Title Case; must match L1 |
| E | Resource_Type | 12 | Type (see schema) |
| F | Priority | 10 | Core, Advanced, Core-N, Advanced-N |

---

## Priority

| Priority | Meaning |
|----------|---------|
| Core | Must know |
| Advanced | Learn when needed |
| Core-N | Must know in niche |
| Advanced-N | Specialist in niche |

**Target**: Core 45–55% · Advanced 40–50% · Niche (-N) 10–15%

---

## L1 Categories & Term Counts

| L1 | Terms | L2 (examples) |
|----|-------|----------------|
| DATA | ~310 | Platform, Architecture, Quality, Processing, BI, Governance, Streaming |
| SOFTWARE | ~117 | Architecture, Patterns, Principles |
| CLOUD | ~102 | Infrastructure, Networking, Services, Resilience |
| PROGRAMMING | ~114 | Structures, Algorithms, Complexity |
| PM | ~76 | Project, Product, Agile, Change, Stakeholder |
| BUSINESS | ~51 | Finance, Contracts, Metrics, Risk |
| DEVOPS | ~53 | Deployment, Observability, Reliability, Automation |
| SECURITY | ~51 | Access, Encryption, Testing, Compliance |
| AI/ML | ~45 | Models, Training, Evaluation, Operations, GenAI |
| JUSTICE | ~56 | Bail, Charges, Court, Corrections, Policing, Public Safety |

---

## Validation Values

**L1**: DATA, SOFTWARE, CLOUD, DEVOPS, SECURITY, AI/ML, PM, PROGRAMMING, BUSINESS, JUSTICE  

**Resource_Type**: Technology, Pattern, Practice, Concept, Metric, Role, Framework, Protocol, Process  

**Priority**: Core, Advanced, Core-N, Advanced-N

---

## INDEX Sheet

- **Slicers**: L1, L2, Resource_Type, Priority (multi-select; connect to TermMaster).
- **Table**: Filtered view of TermMaster.
- **Quick links**: Hyperlinks to each L1 category sheet.

---

## Category Sheets (DATA, SOFTWARE, …)

- Frozen header, “← INDEX” in A1, table + auto-filter.
- Column widths: Term 28, Definition 65, L2 12, Type 10, Priority 10.
- Optional user columns: Status (Known/Learning/Review), Notes.

---

## Schema Alignment

- **Structure**: 6 columns; no blank cells; unique Terms.
- **L1/L2**: Values from SCHEMA.csv; L2 valid for chosen L1.
- **Resource_Type, Priority**: From schema.
- **Definitions**: ≤80 chars; no trailing period.
- **Sort**: L1 → L2 → Term (alphabetical within level).

---

## Files

| File | Purpose |
|------|---------|
| TERMINOLOGY_REFERENCE.csv | Source data |
| SCHEMA.csv | Column definitions, L1/L2/Type/Priority rules |
| DEFINITIONS.csv | L1/L2/Priority definitions |
| REFERENCE.md | This document |
| IMPLEMENTATION.md | Build guide |
| EXCEL_FUNCTIONS.md | Counting, search bar formulas |

---

## Shortcuts

| Action | Key |
|--------|-----|
| Search | Ctrl+F |
| Format as table | Ctrl+T |
| Filter toggle | Ctrl+Shift+L |
| Paste values | Ctrl+Shift+V |
| Next/prev sheet | Ctrl+PgDn / Ctrl+PgUp |



## L1 Categories & L2 Subcategories

**Layout**:
```
┌─────────────────────────────────────────────────────────────┐
│  TERMINOLOGY KNOWLEDGE BASE                                 │
├─────────────┬───────────────────────────────────────────────┤
│             │                                               │
│  [SLICERS]  │  [FILTERED TABLE VIEW]                        │
│             │                                               │
│  L1 Category│  Term | Definition | L2 | Type | Priority     │
│  ─────────  │  ──────────────────────────────               │
│  ☐ DATA     │  (filtered results from TermMaster)           │
│  ☐ SOFTWARE │                                               │
│  ☐ CLOUD    │                                               │
│  ☐ DEVOPS   │                                               │
│  ☐ SECURITY │                                               │
│  ☐ AI/ML    │                                               │
│  ☐ PM       │                                               │
│  ☐ PROGRAM  │                                               │
│  ☐ BUSINESS │                                               │
│             │                                               │
│  L2 Subcat  │                                               │
│  ─────────  │                                               │
│  (dynamic)  │                                               │
│             │                                               │
│  Type       │                                               │
│  ─────────  │                                               │
│  ☐ Technology│                                              │
│  ☐ Pattern  │                                               │
│  ☐ Practice │                                               │
│  ☐ Concept  │                                               │
│             │                                               │
│  Priority   │                                               │
│  ─────────  │                                               │
│  ☐ Core     │                                               │
│  ☐ Core-N   │                                               │
│  ☐ Advanced │                                               │
│  ☐ Advanced-N│                                              │
│             │                                               │
├─────────────┴───────────────────────────────────────────────┤
│  QUICK LINKS: DATA | SOFTWARE | CLOUD | DEVOPS | SECURITY  │
│               AI/ML | PM | PROGRAMMING | BUSINESS           │
└─────────────────────────────────────────────────────────────┘
```
