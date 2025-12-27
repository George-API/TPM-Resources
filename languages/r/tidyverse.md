# R Tidyverse Quick Reference Guide

**Scope**: dplyr, tidyr, stringr, lubridate, and tidyverse patterns.

**Purpose**: Use this for data manipulation with tidyverse packages. For base R syntax, see [R Core](core.md).

## Table of Contents

- [Installation](#installation)
- [Core dplyr Verbs](#core-dplyr-verbs)
- [Joins](#joins)
- [Pipe Operator](#pipe-operator)
- [Additional dplyr Functions](#additional-dplyr-functions)
- [tidyr Essentials](#tidyr-essentials)
- [stringr Essentials](#stringr-essentials)
- [lubridate Essentials](#lubridate-essentials)
- [Common Patterns](#common-patterns)

---

## Installation

```r
install.packages(c("dplyr", "tidyr", "readr", "stringr", "lubridate", "forcats"))

library(dplyr)
library(tidyr)
```

---

## Core dplyr Verbs

### filter() - Filter Rows

```r
df %>% filter(age > 25)
df %>% filter(age > 25 & name == "Alice")
df %>% filter(age > 25 | score > 80)
df %>% filter(!is.na(value))
```

### select() - Select Columns

```r
df %>% select(id, name, age)
df %>% select(-id)                    # exclude column
df %>% select(starts_with("x_"))       # select by prefix
df %>% select(ends_with("_id"))         # select by suffix
df %>% select(contains("name"))        # select by substring
df %>% select(matches("^x_"))          # select by regex
```

### mutate() - Create/Modify Columns

```r
df %>% mutate(total = a + b)
df %>% mutate(total = a + b, 
              avg = total / 2)
df %>% mutate(across(c(a, b), ~ .x * 2))  # modify multiple columns
```

### arrange() - Sort Rows

```r
df %>% arrange(age)
df %>% arrange(desc(age))              # descending
df %>% arrange(age, name)               # multiple columns
```

### summarize() / summarise() - Aggregate

```r
df %>% summarize(mean_age = mean(age))
df %>% summarize(mean_age = mean(age, na.rm = TRUE),
                 count = n())
```

### group_by() - Group Operations

```r
df %>% group_by(category) %>%
  summarize(mean_value = mean(value),
            count = n())
```

### distinct() - Unique Rows

```r
df %>% distinct()
df %>% distinct(category, .keep_all = TRUE)
```

### slice() - Select Rows by Position

```r
df %>% slice(1:5)                       # first 5 rows
df %>% slice_head(n = 10)               # first n rows
df %>% slice_tail(n = 10)                # last n rows
df %>% slice_sample(n = 10)              # random sample
```

---

## Joins

```r
# Inner join
left_join(df1, df2, by = "id")
inner_join(df1, df2, by = "id")
left_join(df1, df2, by = c("id1" = "id2"))  # different key names

# Other joins
right_join(df1, df2, by = "id")
full_join(df1, df2, by = "id")
anti_join(df1, df2, by = "id")          # rows in df1 not in df2
semi_join(df1, df2, by = "id")          # rows in df1 that match df2
```

---

## Pipe Operator

```r
# %>% pipe (forward pipe from magrittr)
df %>%
  filter(age > 25) %>%
  select(name, age) %>%
  arrange(age)

# |> pipe (base R 4.1+)
df |>
  filter(age > 25) |>
  select(name, age) |>
  arrange(age)
```

---

## Additional dplyr Functions

### count() - Count by Group

```r
df %>% count(category)
df %>% count(category, sort = TRUE)
```

### rename() - Rename Columns

```r
df %>% rename(new_name = old_name)
```

### relocate() - Move Columns

```r
df %>% relocate(new_col, .before = name)
```

### case_when() - Vectorized If-Else

```r
df %>% mutate(status = case_when(
  score >= 90 ~ "A",
  score >= 80 ~ "B",
  score >= 70 ~ "C",
  TRUE ~ "F"
))
```

### across() - Apply Function to Multiple Columns

```r
df %>% mutate(across(c(a, b), ~ .x * 2))
df %>% summarize(across(where(is.numeric), mean, na.rm = TRUE))
```

---

## tidyr Essentials

### pivot_longer() - Wide to Long

```r
df %>% pivot_longer(cols = c(x, y, z), 
                    names_to = "variable", 
                    values_to = "value")
```

### pivot_wider() - Long to Wide

```r
df %>% pivot_wider(names_from = variable, 
                   values_from = value)
```

### separate() - Split Column

```r
df %>% separate(name, into = c("first", "last"), sep = " ")
```

### unite() - Combine Columns

```r
df %>% unite("full_name", first, last, sep = " ")
```

### drop_na() - Remove Rows with NA

```r
df %>% drop_na()
df %>% drop_na(age, score)              # specific columns
```

### fill() - Fill NA with Previous Value

```r
df %>% fill(value)
```

---

## stringr Essentials

```r
library(stringr)

str_detect(string, "pattern")           # detect pattern
str_extract(string, "pattern")         # extract first match
str_extract_all(string, "pattern")      # extract all matches
str_replace(string, "old", "new")       # replace first
str_replace_all(string, "old", "new")   # replace all
str_remove(string, "pattern")           # remove pattern
str_sub(string, 1, 3)                  # substring
str_length(string)                     # length
str_to_upper(string)                   # uppercase
str_to_lower(string)                   # lowercase
str_trim(string)                       # trim whitespace
str_split(string, ",")                 # split string
str_c("a", "b", sep = "-")             # concatenate
```

---

## lubridate Essentials

```r
library(lubridate)

# Parse dates
ymd("2025-01-15")
mdy("01/15/2025")
dmy("15-01-2025")

# Extract components
year(date)
month(date)
day(date)
wday(date)                              # day of week
hour(datetime)
minute(datetime)

# Arithmetic
date + days(1)
date + months(1)
date + years(1)
date %--% date2                         # interval
```

---

## Common Patterns

### Split-Apply-Combine Pattern

```r
df %>%
  group_by(category) %>%
  summarize(
    mean_value = mean(value, na.rm = TRUE),
    count = n(),
    .groups = "drop"
  )
```

### Conditional Mutate

```r
df %>%
  mutate(status = ifelse(score >= 60, "pass", "fail"))
```

### Filter and Summarize

```r
df %>%
  filter(!is.na(value)) %>%
  group_by(category) %>%
  summarize(total = sum(value))
```

### Join and Mutate

```r
df1 %>%
  left_join(df2, by = "id") %>%
  mutate(new_col = col1 + col2)
```

### Complex Pipeline

```r
df %>%
  filter(age > 25) %>%
  select(id, name, age, score) %>%
  mutate(status = case_when(
    score >= 90 ~ "A",
    score >= 80 ~ "B",
    TRUE ~ "C"
  )) %>%
  group_by(status) %>%
  summarize(
    count = n(),
    mean_age = mean(age),
    .groups = "drop"
  ) %>%
  arrange(desc(count))
```

