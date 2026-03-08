# Excel Power Query Reference

**Focus**: Power Query M language syntax, data transformation methods, and ETL patterns in Excel.

## Table of Contents

- [Getting Started](#getting-started)
- [M Language Basics](#m-language-basics)
- [Common Transformations](#common-transformations)
- [Data Sources](#data-sources)
- [Query Editor Operations](#query-editor-operations)
- [Advanced Patterns](#advanced-patterns)

---

## Getting Started

**Access**: Data → Get Data → From Other Sources → Blank Query (opens Power Query Editor)

**Query Editor**: Transform data visually, generates M code automatically

**M Language**: Functional language for data transformations, each step is a transformation function

**Applied Steps**: Each transformation creates a new step, can edit M code directly in Advanced Editor

---

## M Language Basics

### Data Types

- **Primitive**: `number`, `text`, `logical` (true/false), `date`, `time`, `datetime`, `duration`
- **Structured**: `list` `{1, 2, 3}`, `record` `[Name="John", Age=30]`, `table`
- **Null**: `null` - missing value

### Variables & Let Expression

```m
let
    x = 10,
    y = 20,
    result = x + y
in
    result
```

### Functions

- **Lambda**: `(x) => x * 2`
- **Named**: `Add = (x, y) => x + y`
- **Usage**: `Add(5, 3)`

### Table Operations

- **Access column**: `Table[ColumnName]` or `Table[#"Column Name"]`
- **Add column**: `Table.AddColumn(Table, "NewColumn", each [Column] * 2)`
- **Select columns**: `Table.SelectColumns(Table, {"Column1", "Column2"})`
- **Remove columns**: `Table.RemoveColumns(Table, {"Column1"})`

---

## Common Transformations

### Filtering Rows

- **Filter**: `Table.SelectRows(Table, each [Column] > 100)`
- **Remove rows**: `Table.RemoveRows(Table, 0, 5)` - Remove 5 rows starting at index 0
- **Keep rows**: `Table.FirstN(Table, 10)` - First N rows
- **Skip rows**: `Table.Skip(Table, 5)` - Skip first N rows

**Common conditions**: `[Column] > 100`, `[Column] = "Value"`, `[Column] <> null`, `Text.Contains([Column], "text")`

### Adding Columns

- **Custom column**: `Table.AddColumn(Table, "NewColumn", each [Column1] + [Column2])`
- **Conditional**: `Table.AddColumn(Table, "Status", each if [Value] > 100 then "High" else "Low")`
- **Index**: `Table.AddIndexColumn(Table, "Index", 0, 1)`
- **From examples**: Query Editor → Add Column → From Examples (AI-powered)

### Modifying Columns

- **Change type**: `Table.TransformColumnTypes(Table, {{"Column", type number}})`
- **Rename**: `Table.RenameColumns(Table, {{"OldName", "NewName"}})`
- **Replace values**: `Table.ReplaceValue(Table, "Old", "New", Replacer.ReplaceText, {"Column"})`
- **Split**: `Table.SplitColumn(Table, "Column", Splitter.SplitTextByDelimiter("-"), {"Col1", "Col2"})`
- **Extract**: `Table.TransformColumns(Table, {{"Column", each Text.Start(_, 5), type text}})`

### Sorting & Grouping

- **Sort**: `Table.Sort(Table, {{"Column", Order.Ascending}})`
- **Group**: `Table.Group(Table, {"Category"}, {{"Sum", each List.Sum([Amount]), type number}})`
- **Distinct**: `Table.Distinct(Table, {"Column"})`

### Combining Data

- **Append**: `Table.Combine({Table1, Table2})` - Stack tables vertically
- **Merge**: `Table.NestedJoin(Table1, {"Key"}, Table2, {"Key"}, "NewColumn", JoinKind.Inner)`
- **Join kinds**: `JoinKind.Inner`, `JoinKind.LeftOuter`, `JoinKind.RightOuter`, `JoinKind.FullOuter`
- **Expand merged**: `Table.ExpandTableColumn(Expanded, "NewColumn", {"Column1", "Column2"})`

---

## Data Sources

### File Sources

- **Excel**: `Excel.Workbook(File.Contents("path.xlsx"), null, true)`
- **CSV**: `Csv.Document(File.Contents("path.csv"), [Delimiter=",", Encoding=65001])`
- **JSON**: `Json.Document(File.Contents("path.json"))`
- **XML**: `Xml.Tables(File.Contents("path.xml"))`
- **Text**: `Lines.FromBinary(File.Contents("path.txt"))`

### Database Sources

- **SQL Server**: Data → Get Data → From Database → From SQL Server Database
- **Access**: Data → Get Data → From Database → From Microsoft Access Database
- **Oracle**: Data → Get Data → From Database → From Oracle Database

### Web & API

- **Web**: `Web.Contents("https://api.example.com/data")`
- **REST API**: Data → Get Data → From Other Sources → From Web → Enter URL

### Folder

- **From Folder**: Data → Get Data → From File → From Folder → Combine files

---

## Query Editor Operations

### Transform Tab

- **Remove Columns**: Select columns → Remove Columns
- **Change Type**: Select column → Data Type dropdown
- **Rename**: Double-click column header
- **Replace Values**: Select column → Replace Values
- **Split Column**: Select column → Split Column → By Delimiter/By Number of Characters
- **Group By**: Transform → Group By
- **Pivot/Unpivot**: Transform → Pivot Column / Unpivot Columns

### Add Column Tab

- **Custom Column**: Add Column → Custom Column → Enter formula
- **Conditional Column**: Add Column → Conditional Column
- **Index Column**: Add Column → Index Column
- **From Examples**: Add Column → From Examples (AI pattern detection)

### Home Tab

- **Close & Load**: Load to worksheet
- **Close & Load To**: Choose destination (Table, PivotTable, Connection only)
- **Refresh**: Refresh query data
- **Advanced Editor**: View/edit M code directly

---

## Advanced Patterns

### Conditional Logic

- **If-else**: `if [Value] > 100 then "High" else "Low"`
- **Multiple conditions**: `if [Value] > 100 then "High" else if [Value] > 50 then "Medium" else "Low"`
- **Null handling**: `if [Column] <> null then [Column] else 0`

### List Operations

- **Aggregation**: `List.Sum({1, 2, 3})`, `List.Average({1, 2, 3})`, `List.Count({1, 2, 3})`
- **Transform**: `List.Transform({1, 2, 3}, each _ * 2)`
- **Filter**: `List.Select({1, 2, 3}, each _ > 1)`

### Text Operations

- **Search**: `Text.Contains([Column], "text")`, `Text.StartsWith([Column], "prefix")`, `Text.EndsWith([Column], "suffix")`
- **Modify**: `Text.Replace([Column], "old", "new")`, `Text.Split([Column], "-")`, `Text.Trim([Column])`
- **Case**: `Text.Upper([Column])`, `Text.Lower([Column])`

### Date Operations

- **Add**: `Date.AddDays([Date], 7)`, `Date.AddMonths([Date], 1)`
- **Extract**: `Date.Year([Date])`, `Date.Month([Date])`, `Date.Day([Date])`, `Date.DayOfWeek([Date])`
- **Difference**: `Duration.Days([EndDate] - [StartDate])`
- **Date literal**: `#date(2025, 1, 1)`

### Error Handling

- **Try-otherwise**: `try [Column] otherwise 0` - Returns value or default
- **Replace errors**: `Table.ReplaceErrorValues(Table, {{"Column", 0}})`

### Parameter Queries

- **Create parameter**: Home → Manage Parameters → New Parameter
- **Use parameter**: Reference as `ParameterName` in queries
- **Dynamic filtering**: `Table.SelectRows(Table, each [Date] >= StartDate)`

### Custom Functions

- **Define**: `let MultiplyByTwo = (x) => x * 2 in MultiplyByTwo`
- **Use**: `Table.AddColumn(Table, "Doubled", each MultiplyByTwo([Value]))`

---

## Common Patterns

### Data Cleaning

- **Remove duplicates**: `Table.Distinct(Table, {"KeyColumn"})`
- **Fill down**: `Table.FillDown(Table, {"Column"})`
- **Remove empty rows**: `Table.SelectRows(Table, each not List.IsEmpty(Record.FieldValues(_)))`
- **Trim all text**: `Table.TransformColumns(Table, List.Transform(Table.ColumnNames(Table), each {_, Text.Trim, type text}))`

### Pivot/Unpivot

- **Pivot** (wide to long): `Table.Pivot(Table, {"ValueColumn"}, "AttributeColumn", "ValueColumn", List.Sum)`
- **Unpivot** (long to wide): `Table.Unpivot(Table, {"Keep1", "Keep2"}, "Attribute", "Value")`

### Merging Queries

- **Inner join**: `Table.NestedJoin(Table1, {"Key"}, Table2, {"Key"}, "NewColumn", JoinKind.Inner)`
- **Left join**: `Table.NestedJoin(Table1, {"Key"}, Table2, {"Key"}, "NewColumn", JoinKind.LeftOuter)`
- **Expand**: `Table.ExpandTableColumn(Expanded, "NewColumn", {"Column1", "Column2"})`

### Conditional Aggregation

- **Group**: `Table.Group(Table, {"Category"}, {{"Total", each List.Sum([Amount]), type number}, {"Count", each Table.RowCount(_), type number}})`

### Date Range Filtering

- **Filter by date**: `Table.SelectRows(Table, each [Date] >= #date(2025, 1, 1) and [Date] <= #date(2025, 12, 31))`

---

> **Note**: Power Query uses M language for transformations. Most operations can be done via Query Editor UI, which generates M code. Use Advanced Editor to view/edit M code directly. Power Query is available in Excel 2016+ (Get & Transform Data) and Excel 365 (Power Query).
