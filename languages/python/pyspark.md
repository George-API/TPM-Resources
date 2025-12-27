# PySpark Quick Reference Guide

## Table of Contents

- [Rapid Lookup](#rapid-lookup)
- [Common Patterns](#common-patterns)

---

## Rapid Lookup

**Quick Syntax Reference**: `spark.read.table()` - Read table | `df.write.format("delta")` - Write Delta | `df.withColumn()` - Add column | `df.filter()` - Filter rows | `df.groupBy().agg()` - Aggregate | `df.join()` - Join tables | `F.col()` - Column reference | `Window.partitionBy()` - Window function

**Note**: `F` = pyspark.sql.functions, `T` = pyspark.sql.types, `spark` = SparkSession

### Spark Session & Config
- `spark` - Fabric notebooks provide `spark` session (no builder needed)
- `spark.conf.set("spark.sql.shuffle.partitions", "200")` - Tune shuffle partitions
- `spark.conf.set("spark.sql.adaptive.enabled", "true")` - Enable adaptive query execution
- `spark.conf.set("spark.sql.files.maxPartitionBytes", 134217728)` - Set file split size (~128MB)

### Read Operations
- `spark.read.table("Sales")` - Read Lakehouse table
- `spark.read.table("lakehouse_name.dbo.FactSales")` - Read with explicit Lakehouse/schema
- `spark.read.format("delta").load("Tables/Sales")` - Read Delta table by path
- `spark.read.parquet("Files/raw/parquet/")` - Read Parquet files
- `spark.read.option("header","true").csv("Files/raw/csv/")` - Read CSV with header
- `spark.read.option("multiline","true").json("Files/raw/json/")` - Read multi-line JSON
- `spark.read.schema(schema).csv("path")` - Read with explicit schema
- `spark.read.format("delta").option("readChangeFeed","true").table("Sales")` - Read Delta change feed

### Write Operations
- `df.write.format("delta").mode("overwrite").save("Tables/CleanedSales")` - Overwrite Delta table
- `df.write.mode("overwrite").option("overwriteSchema","true").saveAsTable("Cleaned")` - Overwrite with schema evolution
- `df.write.mode("append").saveAsTable("FactSales")` - Append to table
- `df.write.partitionBy("Year","Month").format("delta").mode("append").save("Tables/FactSales")` - Partitioned write
- `df.write.option("compression","snappy").parquet("Files/stage/parquet/")` - Write Parquet with compression
- `df.write.format("delta").mode("overwrite").option("mergeSchema","true").save("Tables/DimCustomer")` - Merge schema on write

### Column Operations
- `df.select("col1", "col2")` - Select columns
- `df.select(F.col("col1"))` - Select with column reference
- `df.withColumn("new_col", F.lit(1))` - Add literal column
- `df.withColumn("double_col", F.col("col").cast("double"))` - Cast column type
- `df.withColumnRenamed("old", "new")` - Rename column
- `df.drop("col1", "col2")` - Drop columns

### String Operations
- `df.withColumn("name_trim", F.trim("name"))` - Trim whitespace
- `df.withColumn("name_lower", F.lower("name"))` - To lowercase
- `df.withColumn("name_upper", F.upper("name"))` - To uppercase
- `df.withColumn("name_repl", F.regexp_replace("name", "old", "new"))` - Regex replace
- `df.withColumn("name_sub", F.substring("name", 1, 3))` - Substring
- `df.withColumn("name_split", F.split("name", " "))` - Split into array
- `df.withColumn("concat_col", F.concat(F.col("c1"), F.col("c2")))` - Concatenate columns

### Conditional Logic
- `df.withColumn("flag", F.when(F.col("amount") > 0, 1).otherwise(0))` - If/else
- `df.withColumn("band", F.when(F.col("amount") < 100, "LOW").when(F.col("amount") < 1000, "MID").otherwise("HIGH"))` - Multi-branch
- `df.withColumn("coalesced", F.coalesce("col1", "col2", "col3"))` - First non-null

### Null Handling
- `df.na.fill({"col1": 0, "col2": "UNK"})` - Fill nulls by column
- `df.na.drop()` - Drop rows with any null
- `df.na.drop(subset=["c1", "c2"])` - Drop rows if specific cols null
- `df.na.replace(["", "NA"], None, ["col1", "col2"])` - Replace values

### Filtering
- `df.filter(F.col("amount") > 0)` - Filter numeric condition
- `df.filter((F.col("amount") > 0) & (F.col("status") == "OK"))` - Multiple conditions AND
- `df.filter(F.col("status").isin("OK", "PENDING"))` - Filter by set membership
- `df.filter(F.col("name").isNull())` - Filter nulls
- `df.where("amount > 0 AND status = 'OK'")` - Filter with SQL expression

### Deduplication
- `df.distinct()` - Remove fully duplicate rows
- `df.dropDuplicates(["id"])` - Drop duplicates by key columns

### Sorting
- `df.orderBy("col1")` - Order ascending
- `df.orderBy(F.col("col1").desc())` - Order descending
- `df.sort("col1", "col2")` - Sort multiple columns

### Aggregation
- `df.groupBy("key").count()` - Count rows per group
- `df.groupBy("key").agg(F.sum("amount"))` - Sum by group
- `df.groupBy("k1", "k2").agg(F.avg("amount").alias("avg_amt"))` - Multiple keys with alias
- `df.agg(F.sum("amount").alias("total_amt"))` - Global aggregation
- `df.groupBy("key").agg(F.countDistinct("user_id"))` - Distinct count by group

### Joins
- `df.join(df2, "id")` - Inner join on same column name
- `df.join(df2, ["id", "date"], "left")` - Left join with multiple keys
- `df.join(df2, df["id"] == df2["cust_id"], "inner")` - Join on expression
- `df.join(F.broadcast(df_dim), "id", "left")` - Broadcast small dimension
- `df.join(df2, "id", "left_semi")` - Left semi join (filter existence)
- `df.join(df2, "id", "left_anti")` - Left anti join (find missing keys)

### Window Functions
- `Window.partitionBy("key")` - Partition-only window
- `Window.partitionBy("key").orderBy("ts")` - Partition + order window
- `df.withColumn("row_num", F.row_number().over(Window.partitionBy("key").orderBy("ts")))` - Row number
- `df.withColumn("rank", F.rank().over(Window.partitionBy("key").orderBy("ts")))` - Rank with ties
- `df.withColumn("lag_amt", F.lag("amount", 1).over(Window.partitionBy("key").orderBy("ts")))` - Previous value
- `df.withColumn("run_sum", F.sum("amount").over(Window.partitionBy("key").orderBy("ts").rowsBetween(Window.unboundedPreceding, Window.currentRow)))` - Running sum

### Date/Time Functions
- `df.withColumn("date_col", F.to_date("ts_str", "yyyy-MM-dd"))` - Parse string to date
- `df.withColumn("ts_col", F.to_timestamp("ts_str", "yyyy-MM-dd HH:mm:ss"))` - Parse to timestamp
- `df.withColumn("year", F.year("ts_col"))` - Extract year
- `df.withColumn("month", F.month("ts_col"))` - Extract month
- `df.withColumn("day", F.dayofmonth("ts_col"))` - Extract day
- `df.withColumn("date_add_7", F.date_add("date_col", 7))` - Add days
- `df.withColumn("diff_days", F.datediff("end_date", "start_date"))` - Difference in days

### Array/Map/JSON
- `df.withColumn("arr_col", F.array("c1", "c2", "c3"))` - Create array column
- `df.select(F.explode("arr_col").alias("item"))` - Explode array to rows
- `df.withColumn("json_str", F.to_json(F.struct("c1", "c2")))` - Struct to JSON string
- `df.withColumn("parsed_json", F.from_json("json_str", schema))` - JSON string to struct

### Partitioning
- `df.repartition(200)` - Change number of partitions
- `df.repartition("key")` - Repartition by column
- `df.repartition(200, "key")` - Set partitions + partition column
- `df.coalesce(1)` - Reduce partitions without shuffle
- `df.write.option("maxRecordsPerFile", 1_000_000).format("delta").save("Tables/Silver")` - Control file size

### Delta Lake Operations
- `DeltaTable.forPath(spark, "Tables/DimCustomer")` - Access Delta table
- `DeltaTable.forPath(spark, "Tables/DimCustomer").alias("t").merge(stg.alias("s"), "t.id=s.id").whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()` - Merge upsert
- `spark.sql("OPTIMIZE delta.`Tables/FactSales` ZORDER BY (DateKey, CustomerKey)")` - Optimize with ZORDER
- `spark.sql("VACUUM delta.`Tables/FactSales` RETAIN 168 HOURS")` - Vacuum old files

### Data Quality Checks
- `df.select(F.count("*"), F.countDistinct("id")).show()` - Row count + distinct keys
- `df.groupBy("id").count().filter("count > 1").show()` - Duplicate detection
- `df.select([F.sum(F.col(c).isNull().cast("int")).alias(c) for c in df.columns]).show()` - Null profiling
- `df.summary("count","min","max","mean").show()` - Numeric profile

### Inspection & Utilities
- `df.printSchema()` - Print schema
- `df.show()` - Show first 20 rows
- `df.show(50, truncate=False)` - Show more rows, full columns
- `df.describe().show()` - Basic statistics
- `df.explain()` - Show execution plan
- `df.explain(True)` - Detailed execution plan
- `df.count()` - Row count (triggers action)
- `spark.catalog.listTables()` - List tables in catalog

---

## Common Patterns

### Bronze → Silver → Gold (Medallion Architecture)

```python
# Bronze: Raw ingestion
bronze = spark.read.json("Files/raw/events/")

# Silver: Cleaned and deduplicated
silver = (
    bronze
    .withColumn("EventTime", F.to_timestamp("time", "yyyy-MM-dd'T'HH:mm:ss'Z'"))
    .withColumn("EventDate", F.to_date("EventTime"))
    .dropDuplicates(["eventId"])
)
silver.write.partitionBy("EventDate").format("delta").mode("overwrite").save("Tables/EventsSilver")

# Gold: Aggregated for BI
gold = (
    silver
    .groupBy("EventDate", "CustomerId")
    .agg(F.count("*").alias("EventCount"), F.max("EventTime").alias("LastEventTime"))
)
gold.write.mode("overwrite").saveAsTable("EventsDailyAgg")
```

### Delta Merge (Upsert Pattern)

```python
from delta.tables import DeltaTable

dim_target = DeltaTable.forPath(spark, "Tables/DimCustomer")
stg_customers = spark.read.parquet("Files/stage/customers/")

(dim_target.alias("t")
 .merge(stg_customers.alias("s"), "t.CustomerKey = s.CustomerKey")
 .whenMatchedUpdateAll()
 .whenNotMatchedInsertAll()
 .execute())
```

### Warehouse → Lakehouse Sync

```python
# Read from Fabric Warehouse
df = spark.read.table("WarehouseName.dbo.FactSales")

# Sync to Lakehouse
spark.sql("""
CREATE TABLE IF NOT EXISTS FactSales_LH
USING DELTA
AS SELECT * FROM WarehouseName.dbo.FactSales
""")
```

### Schema Enforcement

```python
from pyspark.sql import types as T

schema = T.StructType([
    T.StructField("id", T.StringType(), False),
    T.StructField("amount", T.DoubleType(), True),
    T.StructField("timestamp", T.TimestampType(), True)
])

df = spark.read.schema(schema).option("mode", "FAILFAST").csv("Files/raw/sales/")
```

### Window Function for Ranking

```python
from pyspark.sql import Window

w = Window.partitionBy("CustomerId").orderBy("EventTime")
df = df.withColumn("Rank", F.row_number().over(w))
```

### Helper Functions

```python
def load_bronze_json(path: str):
    return spark.read.json(path)

def overwrite_delta_table(df, table_path: str):
    (df.write
     .format("delta")
     .mode("overwrite")
     .option("overwriteSchema", "true")
     .save(table_path))
```

