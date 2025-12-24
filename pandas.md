# ============================================================
# Pandas for Modern Data Engineering — Ultra-Tight Quick Ref
# ============================================================
# Structure:
#   1) Rapid lookup: ONE pandas syntax/method per line (comment same line)
#   2) Patterns: common ETL/cleaning/join/time-series examples
# ============================================================

import pandas as pd
import numpy as np


# ============================================================
# 1) RAPID LOOKUP (one syntax/method per line)
# ============================================================

pd.Series([1, 2, 3])                                              # create Series
pd.DataFrame({"a": [1, 2], "b": [3, 4]})                           # create DataFrame
pd.Index([1, 2, 3])                                               # create Index
df.copy()                                                         # deep-ish copy for safe transforms
df.head(10)                                                       # first N rows
df.tail(10)                                                       # last N rows
df.sample(5, random_state=0)                                      # random sample rows
df.shape                                                          # (rows, cols)
df.columns                                                        # column Index
df.dtypes                                                         # per-column dtypes
df.info()                                                         # schema + nulls + memory
df.describe()                                                     # numeric summary
df.nunique(dropna=True)                                           # unique counts per col
df.isna().sum()                                                   # null counts per col
df.notna().mean()                                                 # non-null rate per col
df.memory_usage(deep=True).sum()                                  # total memory usage

df["col"]                                                         # select column as Series
df[["a", "b"]]                                                    # select columns as DataFrame
df.loc[rows, cols]                                                # label-based selection
df.iloc[irows, icols]                                             # position-based selection
df.at[row_label, col]                                             # fast scalar access (label)
df.iat[irow, icol]                                                # fast scalar access (pos)
df.filter(like="name")                                            # select cols by substring
df.filter(regex=r"^x_")                                           # select cols by regex
df.select_dtypes(include=["number"])                              # select numeric cols

df.assign(x=lambda x: x["a"] * 2)                                 # add cols (functional style)
df.rename(columns={"old": "new"})                                 # rename columns
df.set_index("id")                                                # set index
df.set_index("id", verify_integrity=True)                         # set index + enforce unique key
df.reset_index(drop=False)                                        # index -> column(s)
df.sort_values(["dt", "id"], ascending=[True, True])              # sort by columns
df.sort_values(["dt", "id"], kind="mergesort")                    # stable sort (safer for dedupe keep-last)
df.sort_index()                                                   # sort by index
df.reindex(columns=["a", "b", "c"])                               # reorder/align columns
df.drop(columns=["tmp"])                                          # drop columns
df.drop(index=[0, 1])                                             # drop rows by index label
df.drop_duplicates()                                              # remove full duplicate rows
df.drop_duplicates(subset=["id"], keep="last")                    # dedupe by key
df.duplicated(subset=["id"], keep=False)                          # boolean duplicate mask
df.clip(lower=0, upper=100)                                       # clamp numeric values
df.where(cond, other=np.nan)                                      # keep where cond else other
df.mask(cond, other=np.nan)                                       # replace where cond true
df.astype({"id": "Int64", "flag": "boolean"})                     # cast types (nullable friendly)
df.convert_dtypes()                                               # convert to best nullable dtypes

pd.to_numeric(df["x"], errors="coerce")                           # parse numeric, bad -> NA
pd.to_datetime(df["dt"], errors="coerce", utc=True)               # parse datetime, bad -> NaT
df["dt"].dt.date                                                  # date component
df["dt"].dt.year                                                  # year component
df["dt"].dt.floor("min")                                          # floor timestamps to freq
df["dt"].dt.floor("15min")                                        # bucket to 15-min windows (dashboards)
df["dt"].dt.tz_convert("UTC")                                     # convert timezone (tz-aware timestamps)

df.isna()                                                         # boolean NA mask
df.dropna()                                                       # drop rows with any NA
df.dropna(subset=["a", "b"])                                      # drop rows with NA in cols
df.fillna(0)                                                      # fill NA with scalar
df.fillna({"a": 0, "b": "unknown"})                               # fill NA by column
df["a"].fillna(df["a"].median())                                  # fill with statistic
df.replace({"": pd.NA, "N/A": pd.NA})                              # replace values
df.infer_objects(copy=False)                                      # convert object cols where possible

df["s"].astype("string")                                          # convert to pandas string dtype
df["s"].str.strip()                                               # trim whitespace
df["s"].str.lower()                                               # lowercase
df["s"].str.replace(r"\s+", " ", regex=True)                      # regex replace
df["s"].str.contains("foo", na=False)                             # substring match
df["s"].str.extract(r"(\d+)", expand=False)                       # regex capture group
df["s"].str.split(",", expand=False)                              # split into lists
df["s"].str.split(" ", n=1, expand=True)                          # split into columns

df.groupby("k").size()                                            # group counts
df.groupby("k")["v"].sum()                                        # group sum
df.groupby(["k1", "k2"]).agg(total=("v", "sum"))                   # named aggregations
df.groupby("k")["v"].transform("mean")                            # broadcast group stat to rows
df.value_counts(dropna=False)                                     # counts of values (Series)
df["k"].value_counts(normalize=True)                              # proportions
df["value"].between(0, 1_000_000)                                 # inclusive range mask (QA)

df.merge(dim, on="k", how="left")                                 # join tables (SQL-style)
df.merge(dim, on="k", how="left", indicator=True)                 # join + audit results via _merge
df.merge(dim, left_on="a", right_on="b", how="left")              # join on different keys
df.join(other.set_index("id"), on="id", how="left")               # join on index
pd.concat([df1, df2], ignore_index=True)                          # row bind (union)
pd.concat([df1, df2], axis=1)                                     # column bind
df.update(df2)                                                    # patch values from df2 (aligned by index/cols)

df.pivot(index="id", columns="metric", values="val")              # reshape long->wide (unique keys)
df.pivot_table(index="id", columns="m", values="v")               # reshape w/ aggregation
df.melt(id_vars=["id"], var_name="metric", value_name="value")    # wide->long

df.set_index("dt").resample("D").sum(numeric_only=True)           # time resample
df.set_index("dt")["v"].rolling("7D").sum()                       # time-based rolling
df["v"].rolling(10).mean()                                        # row-based rolling
pd.Grouper(key="dt", freq="D")                                    # time binning for groupby on a dt col
pd.merge_asof(left, right, on="dt")                               # asof join (nearest prior by default)

df.to_parquet("out.parquet", index=False)                         # write parquet (pyarrow/fastparquet)
pd.read_parquet("in.parquet")                                     # read parquet
df.to_csv("out.csv", index=False, compression="gzip")             # write compressed csv
pd.read_csv("in.csv", usecols=[...], dtype={...}, parse_dates=[...])  # safer/faster CSV ingest
pd.read_csv("in.csv", chunksize=200_000)                          # chunked CSV ingest
pd.read_csv("in.csv", on_bad_lines="skip", engine="python")       # tolerate messy CSV rows
df.to_json("out.jsonl", orient="records", lines=True, compression="gzip")  # compressed JSONL
pd.read_json("in.jsonl", lines=True)                              # read jsonl

pd.json_normalize(data)                                           # flatten nested JSON records
df.eval("x = a + b")                                              # expression eval (vectorized-ish)
df.query("a > 0 and b < 10")                                      # expression filtering
df.pipe(fn)                                                       # functional pipeline composition


# ============================================================
# 2) COMMON / BASIC PATTERNS (usage + understanding)
# ============================================================

# --- Pattern: schema + safe parsing (common ingestion cleanup)
def clean_schema(df: pd.DataFrame) -> pd.DataFrame:
    df = df.copy()
    df["id"] = pd.to_numeric(df["id"], errors="coerce").astype("Int64")
    df["dt"] = pd.to_datetime(df["dt"], errors="coerce", utc=True)
    df["value"] = pd.to_numeric(df["value"], errors="coerce")
    df["category"] = df["category"].astype("string").str.strip().str.lower()
    df = df.dropna(subset=["id", "dt"])
    return df

# --- Pattern: standardize strings + missing values (messy sources)
def standardize_text(df: pd.DataFrame) -> pd.DataFrame:
    df = df.copy()
    s = df["name"].astype("string")
    df["name"] = (
        s.str.strip()
         .str.replace(r"\s+", " ", regex=True)
         .replace({"": pd.NA, "n/a": pd.NA, "null": pd.NA})
    )
    return df

# --- Pattern: de-duplication by business key (keep latest by timestamp)
def dedupe_latest(df: pd.DataFrame, keys=("id",), ts_col="dt") -> pd.DataFrame:
    df = df.sort_values([*keys, ts_col], kind="mergesort")  # stable -> predictable keep-last
    return df.drop_duplicates(subset=list(keys), keep="last")

# --- Pattern: dimension mapping (codes -> labels) with fallback
def map_codes(df: pd.DataFrame, mapping: dict) -> pd.DataFrame:
    df = df.copy()
    df["label"] = df["code"].map(mapping).fillna("unknown")
    return df

# --- Pattern: conditional flags (vectorized rules)
def add_quality_flags(df: pd.DataFrame) -> pd.DataFrame:
    df = df.copy()
    df["is_bad_value"] = df["value"].isna() | (df["value"] < 0)
    df["tier"] = np.select(
        [df["value"] >= 100, df["value"] >= 50],
        ["gold", "silver"],
        default="bronze"
    )
    return df

# --- Pattern: group rollups for BI (facts -> aggregates)
def build_rollups(df: pd.DataFrame) -> pd.DataFrame:
    return (
        df.groupby(["category"], dropna=False)
          .agg(rows=("id", "size"), total=("value", "sum"), avg=("value", "mean"))
          .reset_index()
    )

# --- Pattern: joins with expectations (validate cardinality)
def enrich_with_dim(fact: pd.DataFrame, dim: pd.DataFrame) -> pd.DataFrame:
    out = fact.merge(dim, on="category_id", how="left", validate="m:1", indicator=True)
    # out["_merge"].value_counts(dropna=False)  # quick join-audit (optional)
    return out

# --- Pattern: time-series daily metrics (freshness dashboards)
def daily_metrics(df: pd.DataFrame) -> pd.DataFrame:
    ts = df.set_index("dt").sort_index()
    return (
        ts.resample("D")
          .agg(rows=("id", "size"), total=("value", "sum"))
          .reset_index()
    )

# --- Pattern: top-N per group (rank + filter)
def top_n_per_group(df: pd.DataFrame, group="category", col="value", n=3) -> pd.DataFrame:
    df = df.copy()
    df["_rank"] = df.groupby(group)[col].rank(ascending=False, method="dense")
    return df[df["_rank"] <= n].drop(columns=["_rank"])

# --- Pattern: chunked CSV processing (large file ETL)
def process_large_csv(in_path: str, out_path: str, chunksize=200_000) -> None:
    first = True
    for chunk in pd.read_csv(in_path, chunksize=chunksize, on_bad_lines="skip", engine="python"):
        chunk = clean_schema(chunk)
        chunk = dedupe_latest(chunk, keys=("id", "dt"), ts_col="dt")
        chunk.to_csv(out_path, index=False, mode="w" if first else "a", header=first)
        first = False

# --- Pattern: lightweight “pipeline” composition (read -> transform -> write)
def pipeline(in_path: str, out_path: str) -> None:
    df = pd.read_parquet(in_path)
    df = (df.pipe(clean_schema)
            .pipe(standardize_text)
            .pipe(add_quality_flags))
    df.to_parquet(out_path, index=False)
