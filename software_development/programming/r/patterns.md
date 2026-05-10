# R Patterns & Best Practices

**Focus**: Higher-level patterns, workflows, and best practices that combine concepts from [Core R](core.md), [dplyr](dplyr.md), and [Tidyverse](tidyverse.md).

**Purpose**: Use this as a supplementary guide for real-world R workflows. For syntax reference, see the individual guides.

## Table of Contents

- [Workflow Patterns](#workflow-patterns)
- [Error Handling & Validation](#error-handling--validation)
- [Performance Optimization](#performance-optimization)
- [Data Quality Patterns](#data-quality-patterns)
- [Code Organization](#code-organization)
- [Best Practices](#best-practices)

---

## Workflow Patterns

### Complete Data Pipeline

**Combines**: Base R I/O, dplyr manipulation, tidyr reshaping, stringr cleaning, lubridate dates

```r
library(tidyverse)

# Read and clean
df_clean <- read_csv("data.csv") %>%
  mutate(
    name = str_trim(str_to_lower(name)),
    date = ymd(date_string),
    across(c(id, value), as.numeric)
  ) %>%
  filter(!is.na(id), !is.na(date)) %>%
  distinct(id, .keep_all = TRUE)

# Reshape and analyze
results <- df_clean %>%
  pivot_longer(cols = starts_with("value_"), names_to = "metric", values_to = "value") %>%
  group_by(category, metric) %>%
  summarize(mean = mean(value, na.rm = TRUE), n = n(), .groups = "drop") %>%
  arrange(desc(mean))

write_csv(results, "output.csv")
```

### Conditional Workflows

```r
# Simple: base R ifelse
df$status <- ifelse(df$score >= 60, "pass", "fail")

# Multiple conditions: dplyr case_when
df <- df %>%
  mutate(
    grade = case_when(
      score >= 90 ~ "A", score >= 80 ~ "B", score >= 70 ~ "C", TRUE ~ "F"
    ),
    is_high_performer = score >= 80
  )

# Conditional aggregation
df %>% group_by(category) %>%
  summarize(
    total = sum(value, na.rm = TRUE),
    high_count = sum(value > 100, na.rm = TRUE),
    high_pct = mean(value > 100, na.rm = TRUE)
  )
```

### Safe Data Access

```r
# Check column exists before use
safe_extract <- function(df, col_name, default = NA) {
  if (col_name %in% names(df)) df[[col_name]] else {
    warning("Column '", col_name, "' not found")
    default
  }
}

# Safe aggregation with validation
safe_summary <- function(df, value_col, group_col = NULL) {
  if (!value_col %in% names(df)) stop("Column '", value_col, "' not found")
  result <- df %>%
    {if (!is.null(group_col)) group_by(., !!sym(group_col)) else .} %>%
    summarize(mean = mean(!!sym(value_col), na.rm = TRUE), n = n(), .groups = "drop")
  return(result)
}
```

### Combining Base R and Tidyverse

```r
# Base R: Simple vector operations
mean(x, na.rm = TRUE)
which(x > 2)

# dplyr: Complex data frame operations
df %>% filter(age > 25) %>% group_by(category) %>% summarize(mean_value = mean(value))

# Prefer tidyverse within dplyr pipelines
df %>% mutate(processed = str_to_lower(text_col) %>% str_replace_all("\\s+", " "))
```

---

## Error Handling & Validation

### Robust Function Design

```r
process_data <- function(df, required_cols = NULL) {
  if (!is.data.frame(df)) stop("Input must be a data frame")
  if (nrow(df) == 0) { warning("Data frame is empty"); return(df) }
  
  if (!is.null(required_cols)) {
    missing <- setdiff(required_cols, names(df))
    if (length(missing) > 0) stop("Missing columns: ", paste(missing, collapse = ", "))
  }
  
  tryCatch({
    df %>% filter(!is.na(!!sym(required_cols[1]))) %>% mutate(processed = TRUE)
  }, error = function(e) {
    warning("Error: ", e$message)
    return(df)
  })
}
```

### Safe Operations

```r
safe_mean <- function(x, default = NA_real_) {
  if (length(x) == 0 || all(is.na(x))) return(default)
  mean(x, na.rm = TRUE)
}

safe_divide <- function(a, b, default = NA_real_) {
  if (length(b) == 0 || b == 0 || is.na(b)) return(default)
  a / b
}

# Use in pipelines
df %>% group_by(category) %>%
  summarize(avg = safe_mean(value), ratio = safe_divide(sum(value), n()))
```

### Data Validation

```r
validate_data <- function(df) {
  issues <- list()
  required <- c("id", "date", "value")
  missing <- setdiff(required, names(df))
  if (length(missing) > 0) issues$missing_columns <- missing
  if (any(duplicated(df$id))) issues$duplicates <- sum(duplicated(df$id))
  if (any(is.na(df$value))) issues$missing_values <- sum(is.na(df$value))
  if (!is.numeric(df$value)) issues$wrong_type <- "value should be numeric"
  return(issues)
}

# Use validation
issues <- validate_data(df)
if (length(issues) > 0) warning("Data quality issues found"); print(issues)
```

---

## Performance Optimization

### When to Use Each Tool

```r
# Small-medium datasets (< 1M rows): dplyr (readable, fast enough)
df %>% filter(condition) %>% group_by(category) %>% summarize(total = sum(value))

# Large datasets (> 1M rows): Consider data.table
library(data.table)
dt <- as.data.table(df)
dt[condition, .(total = sum(value)), by = category]

# Very large: Process in chunks
process_chunks <- function(file_path, chunk_size = 100000) {
  con <- file(file_path, "r")
  header <- readLines(con, n = 1)
  while (TRUE) {
    chunk <- readLines(con, n = chunk_size)
    if (length(chunk) == 0) break
    df_chunk <- read_csv(I(chunk), col_names = strsplit(header, ",")[[1]])
    process_chunk(df_chunk)
  }
  close(con)
}
```

### Vectorization

```r
# Bad: Growing object in loop
result <- c()
for (i in 1:length(x)) result[i] <- x[i] * 2

# Good: Vectorized
result <- x * 2

# Bad: Row-wise loop
for (i in 1:nrow(df)) df$new[i] <- df$a[i] + df$b[i]

# Good: Vectorized columns
df$new <- df$a + df$b

# When needed: rowwise()
df %>% rowwise() %>% mutate(new = sum(a, b, na.rm = TRUE))
```

### Memory Management

```r
# Remove large intermediates
df_processed <- df_large %>% filter(condition) %>% select(needed_cols)
rm(df_large); gc()

# Check memory
object.size(df)
lobstr::obj_size(df)  # More accurate
```

---

## Data Quality Patterns

### Comprehensive Data Cleaning

```r
clean_data <- function(df) {
  df %>%
    mutate(
      across(where(is.character), ~ str_trim(str_to_lower(.x)) %>% str_replace_all("\\s+", " ")),
      across(contains("date"), ymd),
      across(starts_with("id"), as.integer),
      across(where(is.character), ~ na_if(na_if(.x, ""), "n/a"))
    ) %>%
    filter(!is.na(id), complete.cases(select(., id, date))) %>%
    distinct(id, .keep_all = TRUE) %>%
    select(where(~ !all(is.na(.x))))
}
```

### Missing Data Strategy

```r
# Analyze missing
analyze_missing <- function(df) {
  df %>% summarize(across(everything(), ~ sum(is.na(.x)))) %>%
    pivot_longer(everything(), names_to = "column", values_to = "missing_count") %>%
    mutate(
      missing_pct = missing_count / nrow(df) * 100,
      action = case_when(
        missing_pct > 50 ~ "consider_drop",
        missing_pct > 10 ~ "impute",
        TRUE ~ "keep"
      )
    )
}

# Handle missing
handle_missing <- function(df, strategy = "mean") {
  df %>% mutate(across(
    where(is.numeric) & where(~ sum(is.na(.x)) > 0),
    ~ case_when(
      strategy == "mean" ~ replace_na(.x, mean(.x, na.rm = TRUE)),
      strategy == "median" ~ replace_na(.x, median(.x, na.rm = TRUE)),
      strategy == "zero" ~ replace_na(.x, 0),
      TRUE ~ .x
    )
  ))
}
```

### Quality Checks

```r
quality_checks <- function(df) {
  checks <- list()
  checks$completeness <- mean(!is.na(df$critical_col))
  checks$uniqueness <- n_distinct(df$id) / nrow(df)
  if ("date" %in% names(df)) {
    checks$date_range <- all(df$date >= as.Date("2020-01-01") & df$date <= today(), na.rm = TRUE)
  }
  if ("value" %in% names(df)) {
    checks$positive_values <- all(df$value >= 0, na.rm = TRUE)
  }
  return(checks)
}

checks <- quality_checks(df)
if (checks$completeness < 0.9) warning("Low completeness: ", checks$completeness)
```

---

## Code Organization

### Project Structure

```
project/
├── data/raw/          # Raw input data
├── data/processed/    # Cleaned data
├── scripts/
│   ├── 01_load.R      # Load libraries, data
│   ├── 02_clean.R     # Data cleaning
│   ├── 03_analyze.R   # Analysis
│   └── utils.R        # Utility functions
├── output/            # Results
└── README.md
```

### Script Organization

```r
# 01_load.R
library(tidyverse)
options(stringsAsFactors = FALSE, dplyr.summarise.inform = FALSE)
source("scripts/utils.R")
df_raw <- read_csv("data/raw/input.csv")

# 02_clean.R
source("scripts/01_load.R")
df_clean <- clean_data(df_raw)
write_csv(df_clean, "data/processed/cleaned.csv")

# 03_analyze.R
source("scripts/02_clean.R")
results <- df_clean %>% group_by(category) %>% summarize(total = sum(value))
write_csv(results, "output/results.csv")
```

---

## Best Practices

### Naming & Style

- **Variables/Functions**: `snake_case` - `df_customers`, `clean_data()`
- **Data frames**: Prefix with `df_`, `tbl_`, or `dt_` (for data.table)
- **Descriptive names**: `df_customer_orders` not `df1`

```r
# Good: Readable, formatted
df %>% filter(age > 25) %>% group_by(category) %>%
  summarize(mean_value = mean(value, na.rm = TRUE), count = n(), .groups = "drop") %>%
  arrange(desc(mean_value))

# Bad: Hard to read
df %>% filter(age>25) %>% group_by(category) %>% summarize(mean_value=mean(value,na.rm=T),count=n())
```

### Documentation

```r
#' Clean and standardize data frame
#' @param df Data frame to clean
#' @param remove_na Logical, remove rows with any NA? Default FALSE
#' @return Cleaned data frame
clean_data <- function(df, remove_na = FALSE) {
  # Implementation
}
```

### Performance Guidelines

1. **Vectorization**: Prefer vectorized operations over loops
2. **Pre-allocate**: When loops are necessary, pre-allocate results
3. **Choose right tool**: dplyr for readability, data.table for speed on large data
4. **Profile**: Use `profvis::profvis()` to identify bottlenecks
5. **Avoid repeated operations**: Store intermediate results

### Error Prevention

```r
# Always handle NA in aggregations
mean(x, na.rm = TRUE)  # Not: mean(x)

# Check for empty vectors
if (length(x) > 0) result <- mean(x)

# Validate inputs
my_function <- function(x) {
  stopifnot(is.numeric(x), length(x) > 0)
  # Proceed
}

# Safe defaults
safe_operation <- function(x, default = NA) {
  if (length(x) == 0 || all(is.na(x))) return(default)
  # Operation
}
```

### Package Management

```r
# Load only what you need
library(dplyr)  # Not: library(tidyverse) if only using dplyr

# Use namespace when appropriate
dplyr::filter(df, condition)  # Avoids conflicts

# Check availability
if (!requireNamespace("package", quietly = TRUE)) install.packages("package")
library(package)
```

---

> **Note**: This guide supplements the syntax references. For specific function syntax, see [Core R](core.md), [dplyr](dplyr.md), and [Tidyverse](tidyverse.md). This guide focuses on combining these tools effectively in real-world workflows.
