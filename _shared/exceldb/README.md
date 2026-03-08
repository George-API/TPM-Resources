# Excel Knowledge Database

**Purpose**: Source data and guides for building the terminology and programming-syntax workbooks in Excel.

---

## Structure

```
_shared/exceldb/
├── README.md           # This file
├── reference.md        # Column definitions, L1/L2, rules
├── terminology/       # Terminology workbook source
│   ├── terminology_reference.csv   # Main term data
│   ├── definitions.csv             # L1/L2/Priority definitions
│   └── schema.csv                  # Column and L1/Type rules
└── syntax/            # Programming syntax reference
    ├── programming_schema.csv      # Column and L1/L2 rules
    └── programming_syntax.csv     # Syntax items by language
```

---

## Quick start

1. **Terminology workbook** - Import [terminology/terminology_reference.csv](terminology/terminology_reference.csv) into Excel.
2. **Reference** - [reference.md](reference.md) for columns, categories, and rules.
3. **Schema** - Use [terminology/schema.csv](terminology/schema.csv) and [terminology/definitions.csv](terminology/definitions.csv) for L1/L2 and Priority values.

---

## Files

| File | Purpose |
|------|---------|
| [reference.md](reference.md) | Schema, L1/L2, priority, file roles |
| terminology/terminology_reference.csv | Source data for the terminology table |
| terminology/schema.csv | Column definitions, L1/Type/Priority rules |
| terminology/definitions.csv | L1/L2/Priority definitions |
| syntax/programming_schema.csv | Schema for programming syntax table |
| syntax/programming_syntax.csv | Programming syntax reference data |
