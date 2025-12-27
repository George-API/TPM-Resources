# R Quick Reference Guide

## Table of Contents

- [Built-In Syntax](#built-in-syntax)
- [Variables & Basic Types](#variables--basic-types)
- [Operators](#operators)
- [Vectors](#vectors)
- [Lists](#lists)
- [Data Frames](#data-frames)
- [Factors](#factors)
- [Control Flow](#control-flow)
- [Functions](#functions)
- [Packages & Libraries](#packages--libraries)
- [File I/O](#file-io)
- [Common Built-in Functions](#common-built-in-functions)
- [dplyr / Tidyverse Essentials](#dplyr--tidyverse-essentials)
- [Useful Idioms](#useful-idioms)
- [Common Patterns](#common-patterns)

---

## Built-In Syntax

### Values
- `TRUE` / `T` - Boolean true value
- `FALSE` / `F` - Boolean false value
- `NULL` - Null / empty object
- `NA` - Not Available (missing value)
- `NaN` - Not a Number
- `Inf` - Infinity

### Logical / comparison
- `&` - Element-wise logical AND
- `|` - Element-wise logical OR
- `!` - Logical NOT
- `&&` - Short-circuit logical AND (single value)
- `||` - Short-circuit logical OR (single value)
- `==` - Equality
- `!=` - Inequality
- `<`, `>`, `<=`, `>=` - Relational operators
- `%in%` - Membership test

### Conditionals
- `if` - Conditional branch
- `else` - Default branch
- `ifelse()` - Vectorized conditional

### Loops
- `for` - Iterate over sequence
- `while` - Loop while condition true
- `repeat` - Infinite loop (use `break` to exit)
- `break` - Exit loop immediately
- `next` - Skip to next iteration

### Functions
- `function()` - Define function
- `return()` - Return value from function
- `<-` / `=` - Assignment operator

### Error handling
- `try()` - Try expression, catch errors
- `tryCatch()` - Error handling with handlers
- `stop()` - Stop execution with error
- `warning()` - Issue warning
- `message()` - Print message

### Packages
- `library()` - Load package
- `require()` - Load package (returns FALSE if fails)
- `::` - Namespace operator (package::function)

---

## Variables & Basic Types

```r
# Variable assignment
x <- 10              # numeric (double)
y <- 3.14            # numeric
name <- "Alice"      # character
is_valid <- TRUE     # logical
nothing <- NULL      # NULL

# Type checking
class(x)             # "numeric"
typeof(x)            # "double"
is.numeric(x)        # TRUE
is.character(name)   # TRUE
is.logical(is_valid) # TRUE

# Type conversion
as.numeric("42")     # 42
as.character(123)    # "123"
as.logical(1)        # TRUE
as.integer(3.14)     # 3
```

**NA Handling**
- `is.na(x)` - Check for NA
- `is.null(x)` - Check for NULL
- `na.omit()` - Remove NA values
- `na.rm = TRUE` - Remove NA in functions (e.g., `sum(x, na.rm = TRUE)`)

---

## Operators

```r
# Arithmetic
+ - * / %% %/% ^     # add, sub, mul, div, modulo, integer div, power

# Comparison
== != < > <= >=      # equality, inequality, relational

# Logical
& | !                # element-wise logical operators
&& ||                # short-circuit logical operators

# Assignment
<-                   # left assignment (preferred)
=                    # assignment (use in function args)
->                   # right assignment

# Special operators
%in%                 # membership test
%>%                  # pipe operator (from magrittr/dplyr)
```

---

## Vectors

```r
# Create vectors
v <- c(1, 2, 3, 4)           # combine
v <- 1:5                     # sequence 1 to 5
v <- seq(1, 10, by = 2)      # sequence with step
v <- rep(1, 5)               # repeat
v <- vector("numeric", 5)    # empty numeric vector

# Indexing (1-based)
v[1]                         # first element
v[length(v)]                 # last element
v[1:3]                       # first three elements
v[-1]                        # all except first
v[c(1, 3, 5)]               # specific indices
v[v > 2]                     # logical indexing

# Named vectors
v <- c(a = 1, b = 2, c = 3)
v["a"]                       # access by name
names(v)                     # get names

# Operations
length(v)                    # length
sort(v)                      # sort
rev(v)                       # reverse
unique(v)                    # unique values
which(v > 2)                 # indices where condition true
```

---

## Lists

```r
# Create lists
lst <- list(1, "a", TRUE)
lst <- list(a = 1, b = "a", c = TRUE)  # named list

# Access
lst[[1]]                     # first element (returns value)
lst[1]                        # first element (returns list)
lst$a                         # access by name
lst[["a"]]                    # access by name (alternative)

# Modify
lst$new <- "value"           # add element
lst[[1]] <- 999              # modify element

# Operations
length(lst)                  # number of elements
names(lst)                   # get names
unlist(lst)                  # convert to vector (if possible)
```

---

## Data Frames

```r
# Create data frames
df <- data.frame(
  id = 1:3,
  name = c("Alice", "Bob", "Charlie"),
  age = c(25, 30, 35)
)

# Access
df$name                      # column as vector
df[["name"]]                 # column as vector
df[, "name"]                 # column as vector
df[1, ]                      # first row
df[1, 2]                     # row 1, column 2
df[c(1, 3), ]                # rows 1 and 3
df[, c("id", "name")]        # specific columns

# Modify
df$new_col <- 1:3            # add column
df[1, "age"] <- 26           # modify cell

# Operations
nrow(df)                     # number of rows
ncol(df)                     # number of columns
dim(df)                      # dimensions (rows, cols)
names(df)                    # column names
colnames(df)                 # column names
rownames(df)                 # row names
head(df, 10)                 # first 10 rows
tail(df, 10)                 # last 10 rows
str(df)                      # structure
summary(df)                  # summary statistics
```

---

## Factors

```r
# Create factors
f <- factor(c("A", "B", "A", "C"))
f <- factor(c("low", "med", "high"), 
            levels = c("low", "med", "high"), 
            ordered = TRUE)  # ordered factor

# Access
levels(f)                    # get levels
nlevels(f)                   # number of levels
as.numeric(f)                # convert to numeric
as.character(f)              # convert to character
```

---

## Control Flow

### If/Else

```r
if (condition) {
  # code
} else if (another_condition) {
  # code
} else {
  # code
}

# Vectorized ifelse
x <- ifelse(score > 60, "pass", "fail")
```

### For Loops

```r
for (i in 1:5) {
  print(i)
}

for (item in vec) {
  # code
}

for (i in seq_along(vec)) {
  # code with index
}
```

### While Loops

```r
while (condition) {
  # code
  if (exit_condition) break
}
```

### Repeat Loops

```r
repeat {
  # code
  if (condition) break
}
```

---

## Functions

```r
# Define function
function_name <- function(param1, param2) {
  # code
  return(result)
}

# Default arguments
greet <- function(name, greeting = "Hello") {
  paste(greeting, name, sep = ", ")
}

# Variable arguments
sum_all <- function(...) {
  sum(...)
}

# Anonymous function
square <- function(x) x^2
result <- sapply(1:5, function(x) x^2)

# Return multiple values (as list)
stats <- function(x) {
  list(mean = mean(x), sd = sd(x), n = length(x))
}
```

---

## Packages & Libraries

```r
# Install package
install.packages("dplyr")

# Load package
library(dplyr)
require(dplyr)                # returns FALSE if fails

# Use function without loading
dplyr::filter(df, x > 5)

# Check if package is installed
requireNamespace("dplyr")

# List loaded packages
search()

# Get help
help(function_name)
?function_name
??search_term                 # search documentation
```

---

## File I/O

```r
# Read CSV
df <- read.csv("file.csv")
df <- read.csv("file.csv", stringsAsFactors = FALSE)

# Write CSV
write.csv(df, "output.csv", row.names = FALSE)

# Read delimited
df <- read.table("file.txt", sep = "\t", header = TRUE)

# Read RDS (R native format)
obj <- readRDS("file.rds")
saveRDS(obj, "file.rds")

# Read RData
load("file.RData")            # loads objects into environment
save(obj1, obj2, file = "file.RData")

# Read Excel (requires readxl package)
library(readxl)
df <- read_excel("file.xlsx", sheet = 1)

# Write Excel (requires writexl package)
library(writexl)
write_xlsx(df, "output.xlsx")
```

---

## Common Built-in Functions

```r
# Type & conversion
class(x)                      # get class
typeof(x)                     # get type
is.numeric(x), is.character(x), is.logical(x)
as.numeric(x), as.character(x), as.logical(x)

# Math
abs(x)                        # absolute value
sqrt(x)                       # square root
round(x, digits = 2)          # round
ceiling(x)                    # round up
floor(x)                      # round down
log(x), log10(x), exp(x)      # logarithms and exponential

# Aggregation
sum(x, na.rm = TRUE)
mean(x, na.rm = TRUE)
median(x, na.rm = TRUE)
min(x, na.rm = TRUE)
max(x, na.rm = TRUE)
sd(x, na.rm = TRUE)           # standard deviation
var(x, na.rm = TRUE)          # variance
quantile(x, probs = c(0.25, 0.75))

# Sequences
1:10                          # 1 to 10
seq(from = 1, to = 10, by = 2)
seq_along(vec)                # 1:length(vec)
rep(x, times = 5)             # repeat

# String operations
paste("a", "b", sep = "-")    # "a-b"
paste0("a", "b")              # "ab" (no separator)
substr("hello", 1, 3)         # "hel"
nchar("hello")                # 5
grep("pattern", vec)          # find matches
gsub("old", "new", "text")    # replace

# Apply family
apply(matrix, 1, mean)        # apply to rows (1) or cols (2)
lapply(list, function)        # apply to list, returns list
sapply(list, function)        # apply to list, returns vector/matrix
vapply(list, function, FUN.VALUE)  # apply with type checking
tapply(vector, factor, mean)  # apply by group
```

---

## dplyr / Tidyverse Essentials

**Install**: `install.packages(c("dplyr", "tidyr", "readr", "stringr", "lubridate", "forcats"))`

```r
library(dplyr)
library(tidyr)
```

### Core dplyr Verbs

```r
# filter() - filter rows
df %>% filter(age > 25)
df %>% filter(age > 25 & name == "Alice")
df %>% filter(age > 25 | score > 80)
df %>% filter(!is.na(value))

# select() - select columns
df %>% select(id, name, age)
df %>% select(-id)                    # exclude column
df %>% select(starts_with("x_"))       # select by prefix
df %>% select(ends_with("_id"))         # select by suffix
df %>% select(contains("name"))        # select by substring
df %>% select(matches("^x_"))          # select by regex

# mutate() - create/modify columns
df %>% mutate(total = a + b)
df %>% mutate(total = a + b, 
              avg = total / 2)
df %>% mutate(across(c(a, b), ~ .x * 2))  # modify multiple columns

# arrange() - sort rows
df %>% arrange(age)
df %>% arrange(desc(age))              # descending
df %>% arrange(age, name)               # multiple columns

# summarize() / summarise() - aggregate
df %>% summarize(mean_age = mean(age))
df %>% summarize(mean_age = mean(age, na.rm = TRUE),
                 count = n())

# group_by() - group operations
df %>% group_by(category) %>%
  summarize(mean_value = mean(value),
            count = n())

# distinct() - unique rows
df %>% distinct()
df %>% distinct(category, .keep_all = TRUE)

# slice() - select rows by position
df %>% slice(1:5)                       # first 5 rows
df %>% slice_head(n = 10)               # first n rows
df %>% slice_tail(n = 10)                # last n rows
df %>% slice_sample(n = 10)              # random sample
```

### Joins

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

### Pipe Operator

```r
# %>% pipe (forward pipe)
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

### Additional dplyr Functions

```r
# count() - count by group
df %>% count(category)
df %>% count(category, sort = TRUE)

# rename() - rename columns
df %>% rename(new_name = old_name)

# relocate() - move columns
df %>% relocate(new_col, .before = name)

# case_when() - vectorized if-else
df %>% mutate(status = case_when(
  score >= 90 ~ "A",
  score >= 80 ~ "B",
  score >= 70 ~ "C",
  TRUE ~ "F"
))

# across() - apply function to multiple columns
df %>% mutate(across(c(a, b), ~ .x * 2))
df %>% summarize(across(where(is.numeric), mean, na.rm = TRUE))
```

### tidyr Essentials

```r
# pivot_longer() - wide to long
df %>% pivot_longer(cols = c(x, y, z), 
                    names_to = "variable", 
                    values_to = "value")

# pivot_wider() - long to wide
df %>% pivot_wider(names_from = variable, 
                   values_from = value)

# separate() - split column
df %>% separate(name, into = c("first", "last"), sep = " ")

# unite() - combine columns
df %>% unite("full_name", first, last, sep = " ")

# drop_na() - remove rows with NA
df %>% drop_na()
df %>% drop_na(age, score)              # specific columns

# fill() - fill NA with previous value
df %>% fill(value)
```

### stringr Essentials

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

### lubridate Essentials

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

## Useful Idioms

```r
# Check if object exists
exists("var_name")

# Remove object
rm(var_name)
rm(list = ls())                         # remove all

# Check if file exists
file.exists("path/to/file.csv")

# Get working directory
getwd()
setwd("path/to/dir")

# Source R script
source("script.R")

# Set options
options(stringsAsFactors = FALSE)       # don't convert strings to factors

# Check if package is installed
if (!require("dplyr")) install.packages("dplyr")
```

---

## Common Patterns

```r
# Safe column access
if ("column" %in% names(df)) {
  df$column
}

# Apply function to each column
sapply(df, class)                       # get class of each column

# Count NAs per column
colSums(is.na(df))

# Remove rows with any NA
df[complete.cases(df), ]

# Create sequence of dates
seq(as.Date("2025-01-01"), as.Date("2025-12-31"), by = "day")

# Split-apply-combine pattern
df %>%
  group_by(category) %>%
  summarize(
    mean_value = mean(value, na.rm = TRUE),
    count = n(),
    .groups = "drop"
  )

# Conditional mutate
df %>%
  mutate(status = ifelse(score >= 60, "pass", "fail"))

# Filter and summarize
df %>%
  filter(!is.na(value)) %>%
  group_by(category) %>%
  summarize(total = sum(value))

# Join and mutate
df1 %>%
  left_join(df2, by = "id") %>%
  mutate(new_col = col1 + col2)
```

