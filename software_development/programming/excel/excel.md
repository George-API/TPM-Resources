# Excel Quick Reference Guide

## Table of Contents

- [Core Functions](#core-functions)
- [Lookup & Reference](#lookup--reference)
- [Text Functions](#text-functions)
- [Date & Time](#date--time)
- [Logical Functions](#logical-functions)
- [Aggregation](#aggregation)
- [Array Formulas](#array-formulas)
- [Data Analysis](#data-analysis)
- [Common Patterns](#common-patterns)

---

## Core Functions

### Basic Math
- `=SUM(A1:A10)` - Sum range
- `=AVERAGE(A1:A10)` - Average range
- `=MIN(A1:A10)` - Minimum value
- `=MAX(A1:A10)` - Maximum value
- `=COUNT(A1:A10)` - Count numbers
- `=COUNTA(A1:A10)` - Count non-empty cells
- `=COUNTBLANK(A1:A10)` - Count empty cells

### Rounding
- `=ROUND(A1, 2)` - Round to 2 decimals
- `=ROUNDUP(A1, 2)` - Round up
- `=ROUNDDOWN(A1, 2)` - Round down
- `=INT(A1)` - Integer part
- `=TRUNC(A1, 2)` - Truncate to 2 decimals

### Absolute References
- `$A$1` - Absolute row and column
- `$A1` - Absolute column, relative row
- `A$1` - Relative column, absolute row

---

## Lookup & Reference

### VLOOKUP / HLOOKUP
- `=VLOOKUP(lookup_value, table_array, col_index, [range_lookup])`
  - `range_lookup=FALSE` - Exact match (most common)
  - `range_lookup=TRUE` - Approximate match (sorted data)
- `=HLOOKUP(lookup_value, table_array, row_index, [range_lookup])` - Horizontal lookup

### XLOOKUP (Excel 365/2021)
- `=XLOOKUP(lookup_value, lookup_array, return_array, [if_not_found], [match_mode], [search_mode])`
  - More flexible than VLOOKUP, works left-to-right or right-to-left
  - `match_mode`: 0=exact, -1=exact or next smaller, 1=exact or next larger

### INDEX & MATCH
- `=INDEX(array, row_num, [col_num])` - Return value at position
- `=MATCH(lookup_value, lookup_array, [match_type])` - Find position
- `=INDEX(return_array, MATCH(lookup_value, lookup_array, 0))` - Flexible lookup (replaces VLOOKUP)

### Other Lookups
- `=IFERROR(value, value_if_error)` - Return alternative if error
- `=IFNA(value, value_if_na)` - Return alternative if #N/A
- `=CHOOSE(index_num, value1, value2, ...)` - Select from list by index

---

## Text Functions

### Basic Text
- `=CONCATENATE(text1, text2, ...)` - Join text
- `=CONCAT(text1, text2, ...)` - Join text (Excel 2016+)
- `=TEXTJOIN(delimiter, ignore_empty, text1, text2, ...)` - Join with delimiter
- `=LEFT(text, num_chars)` - Extract left characters
- `=RIGHT(text, num_chars)` - Extract right characters
- `=MID(text, start_num, num_chars)` - Extract middle characters
- `=LEN(text)` - Length of text
- `=TRIM(text)` - Remove leading/trailing spaces
- `=CLEAN(text)` - Remove non-printable characters

### Case & Format
- `=UPPER(text)` - Convert to uppercase
- `=LOWER(text)` - Convert to lowercase
- `=PROPER(text)` - Title case (first letter uppercase)
- `=SUBSTITUTE(text, old_text, new_text, [instance_num])` - Replace text
- `=REPLACE(old_text, start_num, num_chars, new_text)` - Replace at position

### Find & Extract
- `=FIND(find_text, within_text, [start_num])` - Find position (case-sensitive)
- `=SEARCH(find_text, within_text, [start_num])` - Find position (case-insensitive)
- `=VALUE(text)` - Convert text to number

---

## Date & Time

### Date Functions
- `=TODAY()` - Current date
- `=NOW()` - Current date and time
- `=DATE(year, month, day)` - Create date
- `=YEAR(date)` - Extract year
- `=MONTH(date)` - Extract month
- `=DAY(date)` - Extract day
- `=WEEKDAY(date, [return_type])` - Day of week (1=Sunday, 2=Monday, etc.)
- `=DATEDIF(start_date, end_date, unit)` - Date difference
  - `unit`: "Y"=years, "M"=months, "D"=days, "MD"=days ignoring years/months

### Date Arithmetic
- `=date + days` - Add days to date
- `=date - days` - Subtract days from date
- `=EDATE(start_date, months)` - Add months
- `=EOMONTH(start_date, months)` - End of month

### Time Functions
- `=TIME(hour, minute, second)` - Create time
- `=HOUR(time)` - Extract hour
- `=MINUTE(time)` - Extract minute
- `=SECOND(time)` - Extract second

---

## Logical Functions

### IF Statements
- `=IF(logical_test, value_if_true, value_if_false)` - Basic conditional
- `=IFS(condition1, value1, condition2, value2, ...)` - Multiple conditions (Excel 2016+)
- `=IFERROR(value, value_if_error)` - Handle errors
- `=IFNA(value, value_if_na)` - Handle #N/A

### Boolean Logic
- `=AND(logical1, logical2, ...)` - All conditions true
- `=OR(logical1, logical2, ...)` - Any condition true
- `=NOT(logical)` - Negate condition
- `=XOR(logical1, logical2, ...)` - Exclusive OR

### Comparison Operators
- `=`, `<>`, `<`, `>`, `<=`, `>=` - Comparison operators

---

## Aggregation

### Conditional Aggregation
- `=SUMIF(range, criteria, [sum_range])` - Sum if condition met
- `=SUMIFS(sum_range, criteria_range1, criteria1, ...)` - Sum with multiple conditions
- `=COUNTIF(range, criteria)` - Count if condition met
- `=COUNTIFS(criteria_range1, criteria1, ...)` - Count with multiple conditions
- `=AVERAGEIF(range, criteria, [average_range])` - Average if condition met
- `=AVERAGEIFS(average_range, criteria_range1, criteria1, ...)` - Average with multiple conditions

### Statistical
- `=MEDIAN(number1, number2, ...)` - Median value
- `=MODE(number1, number2, ...)` - Most frequent value
- `=MODE.SNGL(number1, number2, ...)` - Single mode (Excel 2010+)
- `=STDEV(number1, number2, ...)` - Standard deviation (sample)
- `=STDEV.P(number1, number2, ...)` - Standard deviation (population)
- `=VAR(number1, number2, ...)` - Variance (sample)
- `=VAR.P(number1, number2, ...)` - Variance (population)

---

## Array Formulas

### Dynamic Arrays (Excel 365)
- `=UNIQUE(array)` - Unique values
- `=SORT(array, [sort_index], [sort_order])` - Sort array
- `=FILTER(array, include, [if_empty])` - Filter array
- `=SORTBY(array, by_array, [sort_order])` - Sort by another array

### Legacy Array Formulas
- `{=SUM(IF(condition, values))}` - Array formula (Ctrl+Shift+Enter)
- `=SUMPRODUCT(array1, array2, ...)` - Multiply and sum arrays

---

## Data Analysis

### Pivot Tables
- **Create**: Insert → PivotTable
- **Fields**: Drag fields to Rows, Columns, Values, Filters
- **Values**: Right-click → Value Field Settings → Sum/Average/Count
- **Refresh**: Right-click → Refresh

### Data Validation
- **List**: Data → Data Validation → Allow: List → Source: range
- **Custom**: Data → Data Validation → Allow: Custom → Formula

### Conditional Formatting
- **Highlight Cells**: Home → Conditional Formatting → Highlight Cells Rules
- **Data Bars**: Home → Conditional Formatting → Data Bars
- **Color Scales**: Home → Conditional Formatting → Color Scales
- **Formula-based**: Home → Conditional Formatting → New Rule → Use formula

### Sorting & Filtering
- **Sort**: Data → Sort (multi-level sorting)
- **Filter**: Data → Filter (auto-filter dropdowns)
- **Advanced Filter**: Data → Advanced (complex criteria)

---

## Common Patterns

### Remove Duplicates
- Data → Remove Duplicates → Select columns

### Text to Columns
- Data → Text to Columns → Delimited/Fixed Width

### Find & Replace
- Ctrl+H → Find & Replace dialog
- Use wildcards: `*` (any chars), `?` (single char)

### Named Ranges
- Formulas → Define Name → Create named range
- Use in formulas: `=SUM(MyRange)` instead of `=SUM(A1:A10)`

### Absolute vs Relative References
```excel
=A1        # Relative (changes when copied)
=$A$1      # Absolute (stays same when copied)
=$A1       # Mixed (column absolute, row relative)
=A$1       # Mixed (column relative, row absolute)
```

### Common Formula Patterns
```excel
# Lookup with fallback
=IFERROR(VLOOKUP(A1, Table, 2, FALSE), "Not Found")

# Conditional sum
=SUMIFS(Amount, Category, "A", Date, ">="&DATE(2025,1,1))

# Extract text between delimiters
=MID(A1, FIND("-", A1)+1, FIND("-", A1, FIND("-", A1)+1)-FIND("-", A1)-1)

# Count unique values
=SUMPRODUCT(1/COUNTIF(range, range))

# Last non-empty cell in column
=LOOKUP(2,1/(A:A<>""),A:A)
```

---

## Keyboard Shortcuts

### Navigation
- `Ctrl+Arrow` - Jump to edge of data
- `Ctrl+Home` - Go to A1
- `Ctrl+End` - Go to last used cell
- `F5` - Go To dialog

### Selection
- `Ctrl+Shift+Arrow` - Select to edge
- `Ctrl+A` - Select all
- `Shift+Click` - Select range
- `Ctrl+Click` - Select non-contiguous cells

### Editing
- `F2` - Edit cell
- `Ctrl+Enter` - Fill selected cells with same value
- `Ctrl+D` - Fill down
- `Ctrl+R` - Fill right
- `Ctrl+;` - Insert current date
- `Ctrl+Shift+;` - Insert current time

### Formatting
- `Ctrl+1` - Format Cells dialog
- `Ctrl+B` - Bold
- `Ctrl+I` - Italic
- `Ctrl+U` - Underline

---

> **Note**: Excel version differences: XLOOKUP, UNIQUE, SORT, FILTER require Excel 365/2021. Use VLOOKUP/INDEX+MATCH for older versions.

