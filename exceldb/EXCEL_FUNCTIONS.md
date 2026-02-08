# Excel Functions & Formulas

Lean reference for the Terminology workbook: **counting (all vs filtered)** and a **search bar with dropdown suggestions**.

---

## Counting: All vs Filtered View

| Goal | Formula | Notes |
|------|---------|--------|
| **All** items matching criteria | `=COUNTIF(C:C,"DATA")` | Ignores filters; whole column. |
| **All** items matching multiple criteria | `=COUNTIFS(C:C,"DATA",F:F,"Core")` | AND between conditions. |
| **Filtered** items (visible only) | `=SUBTOTAL(103,C:C)` | 103 = COUNTA visible; 102 = COUNT visible. |
| **Filtered** items matching criteria | See below | SUBTOTAL + criteria. |

**Count visible rows where column C = "DATA"** (works with filters):

```excel
=SUMPRODUCT((SUBTOTAL(103,OFFSET(C2,ROW(C2:C500)-ROW(C2),0)))*(C2:C500="DATA"))
```

- `SUBTOTAL(103, ...)` returns 1 for visible, 0 for hidden per row.
- `OFFSET` gives one cell per row so SUBTOTAL runs per row.
- Multiply by `(C2:C500="DATA")` and sum = count visible DATA rows.

Use same pattern for other criteria, e.g. `(F2:F500="Core")`.

---

## Modern Search Bar with Dropdown Suggestions

**Idea:** One cell = search box. A spilled list = suggestions. A dropdown (or click) picks from suggestions.

**Steps:**

1. **Search cell**  
   e.g. `A1` — user types here (e.g. "Azure").

2. **Spilled suggestions** (Excel 365)  
   In `E2`:
   ```excel
   =FILTER(A2:A500,ISNUMBER(SEARCH($A$1,A2:A500)))
   ```
   Shows only terms where the search text appears (anywhere in the term). Adjust range to your Term column.

3. **Dropdown source**  
   Data Validation on the cell where you want to pick a term:
   - **Allow:** List  
   - **Source:** `=E2#` (or `E2:E500` if you prefer a fixed range)

When the user changes the search in `A1`, the spilled list updates and the dropdown shows only matching terms.

**Optional:** For “starts with” instead of “contains”, use:
```excel
=FILTER(A2:A500,LEFT(A2:A500,LEN($A$1))=$A$1)
```

---

## Quick Reference

| Need | Use |
|------|-----|
| Count all matching one condition | `COUNTIF(range,criteria)` |
| Count all matching multiple conditions | `COUNTIFS(range1,crit1,range2,crit2,...)` |
| Count visible (filtered) rows | `SUBTOTAL(103,range)` |
| Count visible rows matching criteria | `SUMPRODUCT(SUBTOTAL(103,OFFSET(...))*(criteria))` |
| Search suggestions (365) | `FILTER(terms,ISNUMBER(SEARCH(search_cell,terms)))` |
| Dropdown from suggestions | Data Validation List → `=E2#` |

**Shortcuts:** `Ctrl+F` search, `Ctrl+Shift+L` toggle filter, `Ctrl+T` table.
