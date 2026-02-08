# Excel Knowledge Database Implementation Guide

**Approach**: Iterative build — start simple, add complexity only as needed

**Related**: [Reference](REFERENCE.md)

---

## Iteration Overview

| Iteration | What You Get | Time |
|-----------|--------------|------|
| **1. Core** | Single table with filters — functional immediately | 10 min |
| **2. Navigation** | Slicers for visual filtering | 10 min |
| **3. Category Sheets** | Focused views per domain (add as needed) | 5 min each |
| **4. Polish** | Formatting, colors, links (optional) | 15 min |

**Start with Iteration 1.** Only proceed to next iteration when you feel the need.

---

## Iteration 1: Core Table (Start Here)

**Goal**: Get a working, searchable knowledge base in 10 minutes.

### 1.1 Create Workbook
```
1. Open Excel → New Workbook
2. Save as: TERMINOLOGY_KNOWLEDGE.xlsx
```

### 1.2 Import Data
```
1. Data → From Text/CSV
2. Select: exceldb/TERMINOLOGY_REFERENCE.csv
3. Click Load
```

### 1.3 Format as Table
```
1. Click anywhere in data
2. Ctrl+T → Check "My table has headers"
3. Table Design → Table Name: Terms
```

### 1.4 Basic Setup
```
1. Freeze header row:
   - Select Row 2
   - View → Freeze Panes → Freeze Top Row

2. Set column widths:
   - Term: 30
   - Definition: 80 (enable Wrap Text)
   - L1/L2/Type/Priority: 12

3. Sort by L1 → L2 → Term:
   - Data → Sort → Add levels
```

### You're Done With Iteration 1

**What you have now:**
- Full searchable table (Ctrl+F)
- Column filters (click dropdown arrows)
- Sortable by any column
- Auto-expanding when you add rows

**Use it for a few days.** If you find yourself wanting faster filtering, proceed to Iteration 2.

---

## Iteration 2: Add Slicers (When Filtering Feels Slow)

**Goal**: Visual one-click filtering by category.

### 2.1 Insert Slicers
```
1. Click anywhere in Terms table
2. Insert → Slicer
3. Check: L1_Category, L2_Subcategory, Resource_Type, Priority
4. Click OK
```

### 2.2 Position Slicers
```
1. Drag slicers to left side of sheet
2. Stack vertically: L1 on top, L2 middle, Type, Priority bottom
3. Resize to fit (grab corners)
```

### 2.3 Configure Slicers (Optional)
```
Right-click each slicer → Slicer Settings:
- Columns: 1 (vertical list)
- Button height: 0.25"
```

### You're Done With Iteration 2

**What you have now:**
- One-click category filtering
- Multi-select (Ctrl+click for multiple)
- Clear filter (click funnel icon on slicer)

**Use it for a week.** If you want focused study sheets for specific domains, proceed to Iteration 3.

---

## Iteration 3: Category Sheets (Add As Needed)

**Goal**: Create focused sheets only for categories you study frequently.

**Don't create all 9 sheets upfront.** Start with 1-2 you use most.

### 3.1 Create One Category Sheet

```
For your most-used category (e.g., DATA):

1. Filter main table:
   - Click L1 slicer → Select "DATA"

2. Copy visible data:
   - Click in filtered table
   - Ctrl+A (selects visible rows)
   - Ctrl+C

3. Create new sheet:
   - Click + button (new sheet)
   - Rename tab: DATA

4. Paste as values:
   - Click cell A1
   - Right-click → Paste Special → Values
   - Or: Ctrl+Shift+V

5. Format as table:
   - Ctrl+T
   - Freeze Row 1

6. Clear main table filter:
   - Go back to main sheet
   - Clear slicer selection
```

### 3.2 Add More Sheets Later

Repeat 3.1 only when you need another focused view. Suggested order based on typical usage:

| Priority | Category | Why |
|----------|----------|-----|
| High | DATA | Largest, most referenced |
| High | PM | Frequent for TPM work |
| Medium | CLOUD | Infrastructure reference |
| Medium | AI/ML | Growing importance |
| Lower | Others | Add when needed |

### You're Done With Iteration 3

**What you have now:**
- Main sheet with everything + slicers
- Focused category sheet(s) for deep study

**Use it for a month.** If you want visual polish, proceed to Iteration 4.

---

## Iteration 4: Polish (Optional)

**Goal**: Add visual enhancements for easier scanning.

**Only do this after you've validated the structure works for you.**

### 4.1 Color Code L2 Subcategories

```
On category sheets (not main sheet):

1. Select L2 column data
2. Home → Conditional Formatting → New Rule
3. Format cells that contain → Specific Text
4. Add rule for each L2:
   - "Platform" → Light Blue fill
   - "Architecture" → Light Green fill
   - "BI" → Light Purple fill
   - etc.
```

### 4.2 Add Navigation Links (Optional)

```
On each category sheet:
1. Cell A1: Type "← Main"
2. Select A1 → Insert → Link → This Document
3. Select main sheet

On main sheet:
1. Add row above table
2. Type category names as links to each sheet
```

### 4.3 Protect Main Table (Optional)

```
If you want to prevent accidental edits:

1. Go to main sheet
2. Review → Protect Sheet
3. Allow: Select cells, Use AutoFilter

To edit later:
- Review → Unprotect Sheet
```

### 4.4 Hide Columns (Optional)

```
If L1 column is redundant on category sheets:
1. Select column C (L1)
2. Right-click → Hide
```

---

## Daily Usage

### Quick Lookup
```
1. Open workbook
2. Use slicer or Ctrl+F
3. Find term → read definition
```

### Focused Study
```
1. Go to category sheet (if created)
2. Read through terms
3. Use L2 filter to focus on subtopic
```

### Adding New Terms
```
1. Go to main sheet
2. Scroll to bottom of table
3. Add new row (table auto-expands)
4. Fill in all 6 columns (Term, Definition, L1, L2, Type, Priority)
5. Re-sort if desired

If you have category sheets:
- Re-copy to affected category sheet
- Or just add directly to category sheet too
```

---

## When to Add Complexity

| If you notice... | Then add... |
|------------------|-------------|
| Clicking filters is slow | Slicers (Iteration 2) |
| Scrolling past irrelevant categories | Category sheets (Iteration 3) |
| Hard to scan visually | Color coding (Iteration 4) |
| Jumping between sheets is tedious | Navigation links (Iteration 4) |
| Accidentally editing data | Sheet protection (Iteration 4) |

**If you're not feeling the pain, don't add the solution.**

---

## Maintenance

### Weekly (Optional)
```
- Add any new terms learned
- Re-sort main table if added many terms
```

### Monthly
```
- Refresh category sheets if main table changed significantly:
  1. Delete old data in category sheet
  2. Filter main table by L1
  3. Copy → Paste Values to category sheet
  
- Backup file with date:
  TERMINOLOGY_KNOWLEDGE_2026-02.xlsx
```

### When Source CSV Updates
```
If you update TERMINOLOGY_REFERENCE.csv:

Option A - Refresh everything:
1. Delete all sheets except main
2. Re-import CSV
3. Rebuild (faster than it sounds)

Option B - Merge manually:
1. Open CSV, find new terms
2. Add to main table
3. Update category sheets
```

---

## Troubleshooting

| Issue | Quick Fix |
|-------|-----------|
| Slicers not working | Click in table first, then insert slicer |
| Category sheet outdated | Re-copy from filtered main table |
| Lost formatting after paste | Use Paste Special → Values, reformat |
| Table won't expand | Click inside table, not outside |
| Can't find term | Clear all filters first, then Ctrl+F |

---

## Quick Reference

### Keyboard Shortcuts
| Action | Shortcut |
|--------|----------|
| Search | Ctrl+F |
| Select all (in table) | Ctrl+A |
| Format as table | Ctrl+T |
| Paste values | Ctrl+Shift+V |
| Next sheet | Ctrl+Page Down |
| Previous sheet | Ctrl+Page Up |

### Slicer Controls
| Action | How |
|--------|-----|
| Select one | Click item |
| Select multiple | Ctrl+Click items |
| Clear filter | Click funnel icon (top-right of slicer) |
| Clear all slicers | Alt+C (with slicer selected) |

---

## Checklist

### Iteration 1 (Required)
- [ ] Import CSV
- [ ] Format as table
- [ ] Freeze header row
- [ ] Set column widths
- [ ] Sort by L1 → L2 → Term

### Iteration 2 (Recommended)
- [ ] Add L1 slicer
- [ ] Add L2 slicer
- [ ] Add Type slicer
- [ ] Add Priority slicer
- [ ] Position slicers

### Iteration 3 (As Needed)
- [ ] Create DATA sheet
- [ ] Create PM sheet
- [ ] Create other sheets as needed

### Iteration 4 (Optional)
- [ ] Add L2 color coding
- [ ] Add navigation links
- [ ] Protect main sheet
