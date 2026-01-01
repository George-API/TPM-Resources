# Tidyverse Packages Quick Reference

**Scope**: tidyr, stringr, lubridate, readr, forcats, and other tidyverse packages (excluding dplyr).

**Purpose**: Use this for data reshaping, string manipulation, dates, I/O, and factors. For dplyr syntax, see [dplyr](dplyr.md). For base R syntax, see [R Core](core.md).

## Table of Contents

- [Installation](#installation)
- [tidyr - Data Reshaping](#tidyr---data-reshaping)
- [stringr - String Manipulation](#stringr---string-manipulation)
- [lubridate - Date/Time](#lubridate---datetime)
- [readr - Data I/O](#readr---data-io)
- [forcats - Factors](#forcats---factors)
- [Common Patterns](#common-patterns)

---

## Installation

```r
install.packages(c("tidyr", "readr", "stringr", "lubridate", "forcats"))

library(tidyr)
library(readr)
library(stringr)
library(lubridate)
library(forcats)
```

---

## tidyr - Data Reshaping

### pivot_longer() - Wide to Long

```r
df %>% pivot_longer(cols = c(x, y, z), 
                    names_to = "variable", 
                    values_to = "value")

df %>% pivot_longer(cols = starts_with("value_"),
                    names_to = "metric",
                    values_to = "value",
                    names_prefix = "value_")  # remove prefix

df %>% pivot_longer(cols = -id,  # all except id
                    names_to = "variable",
                    values_to = "value")
```

### pivot_wider() - Long to Wide

```r
df %>% pivot_wider(names_from = variable, 
                   values_from = value)

df %>% pivot_wider(names_from = variable,
                   values_from = value,
                   values_fill = 0)  # fill missing with 0

df %>% pivot_wider(names_from = c(category, metric),
                   values_from = value,
                   names_sep = "_")  # combine names with separator
```

### separate() - Split Column

```r
df %>% separate(name, into = c("first", "last"), sep = " ")
df %>% separate(name, into = c("first", "last"), sep = " ", extra = "drop")  # drop extra
df %>% separate(name, into = c("first", "last"), sep = " ", fill = "right")  # fill right
df %>% separate(code, into = c("prefix", "suffix"), sep = 3)  # split at position
df %>% separate(code, into = c("prefix", "suffix"), sep = 3, convert = TRUE)  # convert types
```

### unite() - Combine Columns

```r
df %>% unite("full_name", first, last, sep = " ")
df %>% unite("full_name", first, last, sep = " ", remove = FALSE)  # keep original columns
df %>% unite("code", prefix, suffix, sep = "")  # no separator
```

### drop_na() - Remove Rows with NA

```r
df %>% drop_na()                    # drop rows with any NA
df %>% drop_na(age, score)          # drop rows with NA in specific columns
df %>% drop_na(where(is.numeric))   # drop rows with NA in numeric columns
```

### fill() - Fill NA with Previous/Next Value

```r
df %>% fill(value)                  # fill down (default)
df %>% fill(value, .direction = "down")
df %>% fill(value, .direction = "up")
df %>% fill(value, .direction = "downup")  # try down, then up
df %>% fill(value, .direction = "updown")  # try up, then down
df %>% group_by(category) %>% fill(value)  # fill within groups
```

### nest() / unnest() - Nested Data

```r
df %>% nest(data = c(x, y, z))      # nest columns into list column
df %>% nest(data = -id)              # nest all except id
df %>% unnest(data)                  # unnest list column
df %>% unnest(data, names_sep = "_") # unnest with name prefix
```

### complete() - Complete Combinations

```r
df %>% complete(category, status)   # fill missing combinations
df %>% complete(category, status, fill = list(value = 0))  # fill with value
df %>% complete(category, nesting(id, date))  # complete within nested groups
```

---

## stringr - String Manipulation

### Pattern Detection

```r
str_detect(string, "pattern")       # detect pattern (returns logical)
str_detect(string, "^A")            # starts with
str_detect(string, "Z$")            # ends with
str_detect(string, "\\d+")          # contains digits
str_count(string, "pattern")        # count occurrences
str_which(string, "pattern")        # indices where pattern found
```

### Pattern Extraction

```r
str_extract(string, "pattern")      # extract first match
str_extract_all(string, "pattern")  # extract all matches (returns list)
str_extract_all(string, "pattern", simplify = TRUE)  # returns matrix
str_match(string, "pattern")       # extract with capture groups (returns matrix)
str_match_all(string, "pattern")    # extract all with capture groups
```

### Pattern Replacement

```r
str_replace(string, "old", "new")   # replace first match
str_replace_all(string, "old", "new")  # replace all matches
str_replace(string, "\\d+", "X")    # regex replacement
str_remove(string, "pattern")       # remove pattern
str_remove_all(string, "pattern")   # remove all occurrences
```

### String Manipulation

```r
str_sub(string, 1, 3)               # substring (start, end)
str_sub(string, 1, -1)              # all characters
str_sub(string, -3, -1)             # last 3 characters
str_length(string)                  # length of string
str_pad(string, width = 10)         # pad to width
str_pad(string, width = 10, side = "left", pad = "0")  # pad with zeros
str_trunc(string, width = 10)       # truncate to width
```

### Case Conversion

```r
str_to_upper(string)                # uppercase
str_to_lower(string)                # lowercase
str_to_title(string)                # title case
str_to_sentence(string)             # sentence case
```

### Whitespace

```r
str_trim(string)                    # remove leading/trailing whitespace
str_squish(string)                  # remove extra whitespace (collapses multiple spaces)
```

### Splitting

```r
str_split(string, ",")              # split into list
str_split(string, ",", n = 2)       # split into n parts
str_split_fixed(string, ",", n = 3) # split into fixed number (returns matrix)
str_split_1(string, ",")            # split into vector (single string)
```

### Concatenation

```r
str_c("a", "b", "c")                # concatenate
str_c("a", "b", sep = "-")          # concatenate with separator
str_c("prefix_", 1:3)               # concatenate with vector
str_c("prefix_", 1:3, "_suffix")    # concatenate with prefix and suffix
str_c(c("a", "b"), collapse = ", ") # collapse vector to single string
str_glue("Name: {name}, Age: {age}") # string interpolation (requires glue package)
```

### Pattern Helpers

```r
str_starts(string, "prefix")        # starts with (vectorized)
str_ends(string, "suffix")          # ends with (vectorized)
str_subset(string, "pattern")        # subset strings matching pattern
```

---

## lubridate - Date/Time

### Parsing Dates

```r
ymd("2025-01-15")                   # year-month-day
mdy("01/15/2025")                   # month-day-year
dmy("15-01-2025")                   # day-month-year
ymd_hms("2025-01-15 10:30:00")      # with time
ymd_hm("2025-01-15 10:30")          # without seconds
parse_date_time("2025-01-15", orders = "ymd")  # flexible parsing
```

### Extract Components

```r
year(date)                          # extract year
month(date)                         # extract month
day(date)                           # extract day
wday(date)                          # day of week (1=Sunday)
wday(date, label = TRUE)            # day name
yday(date)                          # day of year
week(date)                          # week of year
quarter(date)                       # quarter
hour(datetime)                      # hour
minute(datetime)                    # minute
second(datetime)                    # second
```

### Date Arithmetic

```r
date + days(1)                      # add days
date + weeks(1)                      # add weeks
date + months(1)                    # add months
date + years(1)                     # add years
date + ddays(1)                     # add duration days (exact)
date + dweeks(1)                    # add duration weeks
date %m+% months(1)                 # add months (rolls over)
date %m-% months(1)                 # subtract months
```

### Intervals & Durations

```r
interval(date1, date2)              # create interval
date1 %--% date2                    # interval operator
as.duration(interval)               # convert to duration
as.period(interval)                 # convert to period
time_length(interval, "years")      # length in units
```

### Rounding

```r
floor_date(date, "month")          # round down to unit
ceiling_date(date, "month")         # round up to unit
round_date(date, "month")           # round to nearest unit
```

### Time Zones

```r
with_tz(datetime, "UTC")            # convert timezone
force_tz(datetime, "UTC")           # force timezone (keep time)
tz(datetime)                        # get timezone
```

---

## readr - Data I/O

### Reading Files

```r
read_csv("file.csv")                # read CSV
read_csv("file.csv", col_types = cols(id = col_integer(), name = col_character()))  # specify types
read_csv("file.csv", na = c("", "NA", "NULL"))  # custom NA values
read_csv("file.csv", skip = 1)      # skip rows
read_csv("file.csv", n_max = 1000)  # read first N rows
read_tsv("file.tsv")                # read tab-separated
read_delim("file.txt", delim = "|") # read custom delimiter
read_table("file.txt")               # read whitespace-separated
```

### Writing Files

```r
write_csv(df, "file.csv")           # write CSV
write_csv(df, "file.csv", na = "")  # custom NA representation
write_tsv(df, "file.tsv")           # write tab-separated
write_delim(df, "file.txt", delim = "|")  # write custom delimiter
```

### Column Types

```r
col_integer()                       # integer
col_double()                        # double
col_character()                     # character
col_logical()                       # logical
col_date(format = "%Y-%m-%d")      # date
col_datetime(format = "%Y-%m-%d %H:%M:%S")  # datetime
col_skip()                          # skip column
col_guess()                         # guess type (default)
```

### Reading with Schema

```r
read_csv("file.csv", 
         col_types = cols(
           id = col_integer(),
           name = col_character(),
           date = col_date(),
           value = col_double()
         ))
```

---

## forcats - Factors

### Creating Factors

```r
as_factor(vector)                   # convert to factor (keeps order)
factor(vector)                     # convert to factor
factor(vector, levels = c("a", "b", "c"))  # specify levels
fct_inorder(vector)                # factor in order of appearance
fct_infreq(vector)                 # factor ordered by frequency
```

### Reordering Levels

```r
fct_relevel(factor, "level1", "level2")  # reorder levels
fct_reorder(factor, value)         # reorder by another variable
fct_reorder2(factor, x, y)         # reorder by two variables
fct_rev(factor)                    # reverse level order
fct_infreq(factor)                 # order by frequency
fct_inorder(factor)                # order by appearance
```

### Recoding Levels

```r
fct_recode(factor, new1 = "old1", new2 = "old2")  # recode levels
fct_collapse(factor, new = c("old1", "old2"))  # collapse levels
fct_lump(factor, n = 5)            # lump small levels into "Other"
fct_lump_min(factor, min = 10)     # lump levels with < min occurrences
fct_lump_prop(factor, prop = 0.05) # lump levels with < prop proportion
```

### Other Operations

```r
fct_drop(factor)                   # drop unused levels
fct_expand(factor, "new_level")    # add new levels
fct_unify(list_of_factors)         # unify levels across factors
fct_count(factor)                  # count levels
fct_unique(factor)                 # unique levels
```

---

## Common Patterns

### Data Reshaping Pipeline

```r
df %>%
  pivot_longer(cols = starts_with("value_"),
               names_to = "metric",
               values_to = "value") %>%
  separate(metric, into = c("type", "period"), sep = "_") %>%
  pivot_wider(names_from = type,
              values_from = value)
```

### String Cleaning Pipeline

```r
df %>%
  mutate(
    name = str_trim(str_to_lower(name)),
    name = str_replace_all(name, "\\s+", " "),
    name = str_remove_all(name, "[^a-z0-9 ]")
  )
```

### Date Processing Pipeline

```r
df %>%
  mutate(
    date = ymd(date_string),
    year = year(date),
    month = month(date, label = TRUE),
    day_of_week = wday(date, label = TRUE),
    days_since = as.numeric(today() - date)
  )
```

### Factor Standardization

```r
df %>%
  mutate(
    category = as_factor(category),
    category = fct_recode(category, 
                         "High" = "H",
                         "Medium" = "M",
                         "Low" = "L"),
    category = fct_relevel(category, "High", "Medium", "Low")
  )
```

### Complete Missing Combinations

```r
df %>%
  complete(category, date, fill = list(value = 0)) %>%
  arrange(category, date)
```

### Fill Missing Values by Group

```r
df %>%
  group_by(category) %>%
  arrange(date) %>%
  fill(value, .direction = "down")
```

### Extract and Parse Text

```r
df %>%
  mutate(
    code = str_extract(string, "\\d+"),
    code = as.integer(code),
    prefix = str_extract(string, "^[A-Z]+")
  )
```

### Date Range Operations

```r
df %>%
  mutate(
    interval = interval(start_date, end_date),
    duration_days = time_length(interval, "days"),
    duration_years = time_length(interval, "years")
  )
```

---

> **Note**: Tidyverse packages work seamlessly together with dplyr. Use `library(tidyverse)` to load all packages at once. Prefer `readr::read_csv()` over base `read.csv()` (faster, better defaults). Use `tidyr` for reshaping, `stringr` for string operations, `lubridate` for dates, and `forcats` for factor manipulation.
