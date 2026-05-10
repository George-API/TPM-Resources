# dplyr Quick Reference Guide

**Focus**: dplyr syntax reference for rapid lookup - data manipulation verbs, functions, and patterns.

## Table of Contents

- [Setup](#setup)
- [Rapid Lookup](#rapid-lookup)
- [Common Patterns](#common-patterns)

---

## Setup

```r
install.packages("dplyr")
library(dplyr)
```

**Note**: dplyr is part of tidyverse. Use `library(tidyverse)` to load dplyr, tidyr, readr, and other packages together.

**Pipe operators** (for chaining function calls, not assignment):
- `%>%` (magrittr pipe) - Passes left side as first argument to right: `df %>% filter(age > 25)` = `filter(df, age > 25)`
- `|>` (native R pipe, R 4.1+) - Base R pipe, same concept, no package needed
- Both work with dplyr. Examples use `%>%` (traditional), but `|>` can be substituted
- **Assignment**: Use `<-` or `=` separately: `result <- df %>% filter(age > 25)`

**Why function calls instead of method notation?**
- R is a **functional programming language** - functions are first-class objects, not methods attached to objects
- dplyr functions are **regular functions** that take data frames as arguments: `filter(df, age > 25)`, not `df.filter(age > 25)`
- R has OOP systems (S3, S4, R6) but uses generic function dispatch, not dot/bracket notation
- Pipes provide method-like chaining syntax: `df %>% filter() %>% select()` reads like `df.filter().select()` but uses functions

---

## Rapid Lookup

**Quick Syntax Reference**: `df %>% verb()` - pipe syntax | `df %>% group_by()` - grouping | `df %>% mutate()` - create/modify | `df %>% filter()` - filter rows | `df %>% select()` - select columns | `df %>% summarize()` - aggregate | `df %>% join()` - joins

### Creation & Inspection
- `tibble(a = 1:3, b = 4:6)` - create tibble (modern data frame)
- `data.frame(a = 1:3, b = 4:6)` - create data frame
- `df %>% glimpse()` - quick view (rows, cols, types)
- `df %>% head(10)` - first N rows
- `df %>% tail(10)` - last N rows
- `df %>% sample_n(5)` - random sample rows
- `df %>% sample_frac(0.1)` - 10% random sample
- `df %>% nrow()` - number of rows
- `df %>% ncol()` - number of columns
- `df %>% dim()` - dimensions (rows, cols)
- `df %>% names()` - column names
- `df %>% str()` - structure

### Selection
- `df %>% select(col1, col2)` - select columns
- `df %>% select(-col1)` - exclude column
- `df %>% select(-c(col1, col2))` - exclude multiple
- `df %>% select(starts_with("x_"))` - select by prefix
- `df %>% select(ends_with("_id"))` - select by suffix
- `df %>% select(contains("name"))` - select by substring
- `df %>% select(matches("^x_"))` - select by regex
- `df %>% select(where(is.numeric))` - select by type
- `df %>% select(where(is.character))` - select by type
- `df %>% select(everything())` - all columns
- `df %>% select(last_col())` - last column
- `df %>% select(col1:col3)` - range of columns
- `df %>% select(col1, everything())` - reorder (col1 first)
- `df %>% pull(col1)` - extract column as vector
- `df %>% pull(col1, id)` - extract with names from id column

### Filtering Rows
- `df %>% filter(age > 25)` - filter by condition
- `df %>% filter(age > 25 & name == "Alice")` - multiple conditions (AND)
- `df %>% filter(age > 25 | score > 80)` - multiple conditions (OR)
- `df %>% filter(!is.na(value))` - not NA
- `df %>% filter(between(age, 25, 65))` - between values
- `df %>% filter(age %in% c(25, 30, 35))` - in set
- `df %>% filter(str_detect(name, "^A"))` - pattern matching
- `df %>% filter(if_all(c(age, score), ~ .x > 0))` - all columns meet condition
- `df %>% filter(if_any(c(age, score), ~ .x > 100))` - any column meets condition
- `df %>% filter(row_number() <= 10)` - first 10 rows
- `df %>% slice(1:5)` - rows by position
- `df %>% slice_head(n = 10)` - first n rows
- `df %>% slice_tail(n = 10)` - last n rows
- `df %>% slice_min(value, n = 5)` - bottom 5 by value
- `df %>% slice_max(value, n = 5)` - top 5 by value
- `df %>% slice_sample(n = 10)` - random sample
- `df %>% slice_sample(prop = 0.1)` - 10% sample
- `df %>% distinct()` - unique rows
- `df %>% distinct(col1, .keep_all = TRUE)` - distinct by column(s)

### Creating/Modifying Columns
- `df %>% mutate(total = a + b)` - create column
- `df %>% mutate(total = a + b, avg = total / 2)` - multiple columns
- `df %>% mutate(across(c(a, b), ~ .x * 2))` - modify multiple columns
- `df %>% mutate(across(where(is.numeric), ~ .x * 2))` - modify all numeric
- `df %>% mutate(rank = row_number())` - row number
- `df %>% mutate(rank = row_number(desc(value)))` - rank by value
- `df %>% mutate(prev = lag(value))` - previous value
- `df %>% mutate(next_val = lead(value))` - next value
- `df %>% mutate(cumsum = cumsum(value))` - cumulative sum
- `df %>% mutate(cummean = cummean(value))` - cumulative mean
- `df %>% mutate(percent_rank = percent_rank(value))` - percentile rank
- `df %>% mutate(dense_rank = dense_rank(value))` - dense rank
- `df %>% mutate(ntile = ntile(value, 4))` - divide into n groups
- `df %>% mutate(status = case_when(score >= 90 ~ "A", score >= 80 ~ "B", TRUE ~ "C"))` - conditional
- `df %>% mutate(status = ifelse(score >= 60, "pass", "fail"))` - simple conditional

### Sorting
- `df %>% arrange(age)` - sort ascending
- `df %>% arrange(desc(age))` - sort descending
- `df %>% arrange(age, name)` - multiple columns
- `df %>% arrange(desc(age), name)` - mixed order

### Aggregation
- `df %>% summarize(mean_age = mean(age))` - single aggregation
- `df %>% summarize(mean_age = mean(age, na.rm = TRUE), count = n())` - multiple aggregations
- `df %>% summarize(n = n())` - count rows
- `df %>% summarize(n_distinct = n_distinct(category))` - count distinct
- `df %>% summarize(first = first(value))` - first value
- `df %>% summarize(last = last(value))` - last value
- `df %>% summarize(nth = nth(value, 3))` - nth value
- `df %>% summarize(sum = sum(value, na.rm = TRUE))` - sum
- `df %>% summarize(mean = mean(value, na.rm = TRUE))` - mean
- `df %>% summarize(median = median(value, na.rm = TRUE))` - median
- `df %>% summarize(sd = sd(value, na.rm = TRUE))` - standard deviation
- `df %>% summarize(min = min(value, na.rm = TRUE))` - minimum
- `df %>% summarize(max = max(value, na.rm = TRUE))` - maximum
- `df %>% summarize(quantile = quantile(value, 0.25, na.rm = TRUE))` - quantile

### Grouping
- `df %>% group_by(category)` - group by column
- `df %>% group_by(category, status)` - multiple groups
- `df %>% group_by(category) %>% summarize(mean_value = mean(value))` - group and aggregate
- `df %>% group_by(category) %>% summarize(mean_value = mean(value), .groups = "drop")` - drop grouping
- `df %>% group_by(category) %>% mutate(rank = row_number(desc(value)))` - rank within group
- `df %>% group_by(category) %>% slice_max(value, n = 3)` - top N per group
- `df %>% ungroup()` - remove grouping
- `df %>% count(category)` - count by group
- `df %>% count(category, sort = TRUE)` - count sorted
- `df %>% count(category, status)` - count by multiple columns
- `df %>% count(category, wt = value)` - weighted count

### Joins
- `df1 %>% left_join(df2, by = "id")` - left join
- `df1 %>% inner_join(df2, by = "id")` - inner join
- `df1 %>% right_join(df2, by = "id")` - right join
- `df1 %>% full_join(df2, by = "id")` - full join
- `df1 %>% left_join(df2, by = c("id1" = "id2"))` - different key names
- `df1 %>% anti_join(df2, by = "id")` - rows in df1 not in df2
- `df1 %>% semi_join(df2, by = "id")` - rows in df1 that match df2

### Combining Data Frames
- `bind_rows(df1, df2)` - stack rows (union)
- `bind_rows(list(df1, df2, df3))` - from list
- `bind_cols(df1, df2)` - combine columns (same rows)

### Column Operations
- `df %>% rename(new_name = old_name)` - rename column
- `df %>% rename(new1 = old1, new2 = old2)` - rename multiple
- `df %>% rename_with(~ paste0("new_", .x), starts_with("old_"))` - rename with function
- `df %>% rename_with(toupper)` - uppercase all names
- `df %>% relocate(new_col, .before = name)` - move column before
- `df %>% relocate(new_col, .after = name)` - move column after
- `df %>% relocate(where(is.numeric), .after = last_col())` - move numeric to end
- `df %>% add_column(new_col = 1)` - add column with constant
- `df %>% add_column(new_col = 1:nrow(.), .before = "name")` - add before column

### Row Operations
- `df %>% add_row(id = 999, name = "New", age = 30)` - add row
- `df %>% slice(1:5)` - select rows by position
- `df %>% slice(-(1:5))` - exclude rows by position

### Window Functions
- `df %>% mutate(rank = row_number())` - sequential row numbers
- `df %>% mutate(rank = rank(value))` - rank (ties get average)
- `df %>% mutate(dense_rank = dense_rank(value))` - dense rank (no gaps)
- `df %>% mutate(percent_rank = percent_rank(value))` - percentile rank
- `df %>% mutate(ntile = ntile(value, 4))` - divide into n groups
- `df %>% mutate(lag = lag(value))` - previous value
- `df %>% mutate(lead = lead(value))` - next value
- `df %>% mutate(cumsum = cumsum(value))` - cumulative sum
- `df %>% mutate(cummean = cummean(value))` - cumulative mean
- `df %>% group_by(category) %>% mutate(rank = row_number(desc(value)))` - rank within group

### Missing Values
- `df %>% filter(!is.na(value))` - filter out NA
- `df %>% drop_na()` - drop rows with any NA
- `df %>% drop_na(age, score)` - drop rows with NA in specific columns
- `df %>% replace_na(list(age = 0, name = "Unknown"))` - replace NA with values
- `df %>% mutate(across(where(is.numeric), ~ replace_na(.x, mean(.x, na.rm = TRUE))))` - fill with mean

### Type Conversion
- `df %>% mutate(across(c(col1, col2), as.numeric))` - convert to numeric
- `df %>% mutate(across(where(is.character), as.factor))` - convert to factor
- `df %>% mutate(across(where(is.numeric), as.character))` - convert to character

### across() - Apply to Multiple Columns
- `df %>% mutate(across(c(a, b), ~ .x * 2))` - transform multiple columns
- `df %>% mutate(across(where(is.numeric), ~ .x * 2))` - transform all numeric
- `df %>% summarize(across(where(is.numeric), mean, na.rm = TRUE))` - summarize all numeric
- `df %>% mutate(across(c(a, b), list(mean = mean, sd = sd)))` - multiple functions
- `df %>% mutate(across(where(is.numeric), ~ .x / max(.x, na.rm = TRUE)))` - normalize

---

## Common Patterns

### Schema Cleanup (Common Ingestion Pattern)

```r
clean_schema <- function(df) {
  df %>%
    mutate(
      id = as.integer(id),
      dt = as.Date(dt),
      value = as.numeric(value),
      category = str_trim(str_to_lower(category))
    ) %>%
    filter(!is.na(id), !is.na(dt))
}
```

### Standardize Strings + Missing Values

```r
standardize_text <- function(df) {
  df %>%
    mutate(
      name = str_trim(str_to_lower(name)) %>%
        str_replace_all("\\s+", " ") %>%
        na_if("") %>%
        na_if("n/a") %>%
        na_if("null")
    )
}
```

### De-duplication by Business Key (Keep Latest)

```r
dedupe_latest <- function(df, keys = "id", ts_col = "dt") {
  df %>%
    arrange(across(all_of(c(keys, ts_col)))) %>%
    distinct(across(all_of(keys)), .keep_all = TRUE)
}
```

### Dimension Mapping with Fallback

```r
map_codes <- function(df, mapping, code_col = "code", label_col = "label") {
  df %>%
    mutate({{label_col}} := recode({{code_col}}, !!!mapping, .default = "unknown"))
}
```

### Conditional Flags (Vectorized Rules)

```r
add_quality_flags <- function(df) {
  df %>%
    mutate(
      is_bad_value = is.na(value) | value < 0,
      tier = case_when(
        value >= 100 ~ "gold",
        value >= 50 ~ "silver",
        TRUE ~ "bronze"
      )
    )
}
```

### Group Rollups for BI

```r
build_rollups <- function(df) {
  df %>%
    group_by(category, .drop = FALSE) %>%
    summarize(
      rows = n(),
      total = sum(value, na.rm = TRUE),
      avg = mean(value, na.rm = TRUE),
      .groups = "drop"
    )
}
```

### Joins with Validation

```r
enrich_with_dim <- function(fact, dim) {
  fact %>%
    left_join(dim, by = "category_id") %>%
    # Validate: check for unmatched rows
    # filter(is.na(dim_col)) %>% count()  # audit unmatched
    identity()
}
```

### Top-N per Group

```r
top_n_per_group <- function(df, group = "category", col = "value", n = 3) {
  df %>%
    group_by(across(all_of(group))) %>%
    slice_max({{col}}, n = n) %>%
    ungroup()
}
```

### Window Functions for Rankings

```r
add_rankings <- function(df) {
  df %>%
    group_by(category) %>%
    mutate(
      rank = row_number(desc(value)),
      percent_rank = percent_rank(value),
      tier = ntile(value, 4)
    ) %>%
    ungroup()
}
```

### Conditional Aggregation

```r
conditional_summary <- function(df) {
  df %>%
    group_by(category) %>%
    summarize(
      total = sum(value, na.rm = TRUE),
      avg = mean(value, na.rm = TRUE),
      count = n(),
      high_count = sum(value > 100, na.rm = TRUE),
      .groups = "drop"
    )
}
```

### Complex Pipeline

```r
pipeline <- function(df) {
  df %>%
    clean_schema() %>%
    standardize_text() %>%
    dedupe_latest(keys = "id", ts_col = "dt") %>%
    add_quality_flags() %>%
    filter(!is_bad_value) %>%
    build_rollups()
}
```

