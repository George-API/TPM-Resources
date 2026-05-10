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
- `Window.partitionBy("key")` - Partition-only window (no ordering)
- `Window.partitionBy("key").orderBy("ts")` - Partition + order window
- `Window.partitionBy("key").orderBy("ts").rowsBetween(Window.unboundedPreceding, Window.currentRow)` - Partition + order + frame
- `F.row_number().over(Window.partitionBy("key").orderBy("ts"))` - Sequential row number (no ties)
- `F.rank().over(Window.partitionBy("key").orderBy("ts"))` - Rank with gaps for ties
- `F.dense_rank().over(Window.partitionBy("key").orderBy("ts"))` - Rank without gaps for ties
- `F.lag("amount", 1).over(Window.partitionBy("key").orderBy("ts"))` - Previous row value
- `F.lead("amount", 1).over(Window.partitionBy("key").orderBy("ts"))` - Next row value
- `F.sum("amount").over(Window.partitionBy("key").orderBy("ts").rowsBetween(Window.unboundedPreceding, Window.currentRow))` - Running sum
- `F.avg("amount").over(Window.partitionBy("key").orderBy("ts"))` - Running average
- `F.first("amount").over(Window.partitionBy("key").orderBy("ts"))` - First value in window
- `F.last("amount").over(Window.partitionBy("key").orderBy("ts"))` - Last value in window

### Date/Time Functions
- `F.to_date("col", "yyyy-MM-dd")` - Parse string to date with format
- `F.to_date("col")` - Parse string to date (auto-detect format)
- `F.to_timestamp("col", "yyyy-MM-dd HH:mm:ss")` - Parse string to timestamp with format
- `F.to_timestamp("col")` - Parse string to timestamp (auto-detect format)
- `F.current_date()` - Current date
- `F.current_timestamp()` - Current timestamp
- `F.year("date_col")` - Extract year
- `F.month("date_col")` - Extract month (1-12)
- `F.dayofmonth("date_col")` - Extract day of month (1-31)
- `F.dayofweek("date_col")` - Extract day of week (1=Sunday, 7=Saturday)
- `F.dayofyear("date_col")` - Extract day of year (1-366)
- `F.weekofyear("date_col")` - Extract week of year (1-53)
- `F.quarter("date_col")` - Extract quarter (1-4)
- `F.hour("timestamp_col")` - Extract hour (0-23)
- `F.minute("timestamp_col")` - Extract minute (0-59)
- `F.second("timestamp_col")` - Extract second (0-59)
- `F.date_add("date_col", 7)` - Add days to date
- `F.date_sub("date_col", 7)` - Subtract days from date
- `F.datediff("end_date", "start_date")` - Difference in days
- `F.months_between("end_date", "start_date")` - Difference in months
- `F.add_months("date_col", 3)` - Add months to date
- `F.date_format("date_col", "yyyy-MM-dd")` - Format date as string
- `F.trunc("date_col", "month")` - Truncate date to unit (year/month/week/day)

### Array/Map/JSON
- `F.array("c1", "c2", "c3")` - Create array column from columns
- `F.array_contains("arr_col", "value")` - Check if array contains value
- `F.size("arr_col")` - Get array size
- `F.explode("arr_col")` - Explode array to rows (one row per element)
- `F.explode_outer("arr_col")` - Explode array (keeps nulls)
- `F.posexplode("arr_col")` - Explode with position index
- `F.array_join("arr_col", ",")` - Join array elements with delimiter
- `F.struct("c1", "c2", "c3")` - Create struct column
- `F.to_json(F.struct("c1", "c2"))` - Convert struct to JSON string
- `F.from_json("json_str", schema)` - Parse JSON string to struct
- `F.get_json_object("json_str", "$.field")` - Extract JSON field by path
- `F.json_tuple("json_str", "field1", "field2")` - Extract multiple JSON fields

### Partitioning
- `df.repartition(200)` - Change number of partitions
- `df.repartition("key")` - Repartition by column
- `df.repartition(200, "key")` - Set partitions + partition column
- `df.coalesce(1)` - Reduce partitions without shuffle
- `df.write.option("maxRecordsPerFile", 1_000_000).format("delta").save("Tables/Silver")` - Control file size

### Delta Lake Operations
- `DeltaTable.forPath(spark, "Tables/DimCustomer")` - Access Delta table by path
- `DeltaTable.forName(spark, "DimCustomer")` - Access Delta table by name
- `DeltaTable.forName(spark, "lakehouse.DimCustomer")` - Access with lakehouse/schema
- `delta_table.merge(source_df, "condition").whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()` - Merge upsert
- `delta_table.merge(source_df, "condition").whenMatchedUpdate(set={"col": "value"}).whenNotMatchedInsert(values={...}).execute()` - Merge with specific updates
- `delta_table.delete("condition")` - Delete rows matching condition
- `delta_table.update("condition", set={"col": "value"})` - Update rows matching condition
- `delta_table.history()` - Show table history (versions)
- `delta_table.vacuum(retentionHours=168)` - Vacuum old files (Python API)
- `spark.sql("OPTIMIZE delta.`Tables/FactSales` ZORDER BY (DateKey, CustomerKey)")` - Optimize with ZORDER
- `spark.sql("OPTIMIZE delta.`Tables/FactSales`")` - Optimize table (compact files)
- `spark.sql("VACUUM delta.`Tables/FactSales` RETAIN 168 HOURS")` - Vacuum old files (SQL)
- `spark.sql("DESCRIBE HISTORY delta.`Tables/FactSales`")` - Show table history (SQL)

### Data Quality Checks
- `df.select(F.count("*").alias("total_rows"))` - Total row count
- `df.select(F.countDistinct("id").alias("unique_ids"))` - Distinct count of column
- `df.groupBy("id").count().filter("count > 1")` - Find duplicate keys
- `df.select([F.sum(F.col(c).isNull().cast("int")).alias(c) for c in df.columns])` - Null count per column
- `df.select([F.count(F.when(F.col(c).isNull(), 1)).alias(c) for c in df.columns])` - Null count per column (alternative)
- `df.summary("count", "min", "max", "mean", "stddev")` - Statistical summary
- `df.describe()` - Basic statistics (count, mean, stddev, min, max)
- `df.select(F.min("date_col"), F.max("date_col"))` - Date range check
- `df.filter(F.col("amount") < 0)` - Find negative values (range violation)
- `df.filter(F.length("id") == 0)` - Find empty strings

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

**Common Methods by Layer:**

**Bronze (Raw Ingestion)**:
- **Read**: `spark.read.json("path")` - Read JSON files
- **Read**: `spark.read.csv("path", header=True)` - Read CSV with header
- **Read**: `spark.read.parquet("path")` - Read Parquet files
- **Read**: `.option("multiline", "true")` - Read multi-line JSON
- **Read**: `.option("mode", "PERMISSIVE")` - Allow malformed records (sent to _corrupt_record)
- **Read**: `.option("columnNameOfCorruptRecord", "_corrupt_record")` - Column name for corrupt records
- **Read**: `.schema(schema)` - Read with explicit schema
- **Metadata**: `.withColumn("_ingestion_timestamp", F.current_timestamp())` - Add ingestion timestamp
- **Metadata**: `.withColumn("_source_file", F.input_file_name())` - Add source file path
- **Write**: `.write.format("delta").mode("append")` - Append to Delta table
- **Write**: `.partitionBy("_ingestion_date")` - Partition by ingestion date
- **Write**: `.save("Tables/Bronze/...")` - Save to path

**Silver (Cleaned & Standardized)**:
- **Filter**: `.filter(F.col("col") > 0)` - Filter rows by condition
- **Filter**: `.where("col > 0")` - Filter with SQL expression
- **Filter**: `.na.drop()` - Drop rows with any null
- **Filter**: `.na.drop(subset=["col1", "col2"])` - Drop rows if specific columns null
- **Transform**: `.withColumn("new_col", F.col("old_col").cast("decimal(18,2)"))` - Cast column type
- **Transform**: `.withColumn("upper", F.upper("col"))` - Convert to uppercase
- **Transform**: `.withColumn("trimmed", F.trim("col"))` - Trim whitespace
- **Transform**: `.withColumn("cleaned", F.regexp_replace("col", "pattern", "replacement"))` - Regex replace
- **Transform**: `.withColumn("coalesced", F.coalesce("col1", "col2", "col3"))` - First non-null value
- **Transform**: `.na.fill({"col1": 0, "col2": "UNK"})` - Fill nulls by column
- **Dedupe**: `.dropDuplicates(["id"])` - Drop duplicates by key columns
- **Dedupe**: `F.row_number().over(Window.partitionBy("key").orderBy("ts"))` - Window function for dedupe
- **Type**: `F.to_timestamp("col", "yyyy-MM-dd HH:mm:ss")` - Parse string to timestamp
- **Type**: `F.to_date("col", "yyyy-MM-dd")` - Parse string to date
- **Type**: `F.col("col").cast("decimal(18,2)")` - Cast to decimal
- **Write**: `.write.format("delta").mode("append")` - Append to Delta table
- **Write**: `.option("mergeSchema", "true")` - Allow schema evolution
- **Write**: `.option("delta.autoOptimize.optimizeWrite", "true")` - Auto-optimize writes
- **Write**: `.partitionBy("EventDate")` - Partition by column

**Gold (Business Logic & Aggregations)**:
- **Join**: `.join(df2, on="id", how="left")` - Left join on column
- **Join**: `.join(df2, ["id", "date"], "inner")` - Inner join on multiple columns
- **Join**: `.join(F.broadcast(df_dim), on="id", how="left")` - Broadcast small dimension
- **Join**: `.join(df2, on="id", how="left_semi")` - Left semi join (filter existence)
- **Join**: `.join(df2, on="id", how="left_anti")` - Left anti join (find missing keys)
- **Aggregate**: `.groupBy("key").agg(F.sum("amount"))` - Sum by group
- **Aggregate**: `.groupBy("key").agg(F.avg("amount"), F.count("*"))` - Multiple aggregations
- **Aggregate**: `.groupBy("key").agg(F.countDistinct("user_id"))` - Distinct count
- **Aggregate**: `.agg(F.sum("amount").alias("total"))` - Global aggregation
- **Calculate**: `.withColumn("revenue", F.col("amount") * F.col("quantity"))` - Multiply columns
- **Calculate**: `.withColumn("flag", F.when(F.col("amount") > 100, 1).otherwise(0))` - Conditional logic
- **Calculate**: `.withColumn("date_90", F.date_sub(F.current_date(), 90))` - Date arithmetic
- **Upsert**: `DeltaTable.forPath(spark, "path").merge(source, "condition").whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()` - Merge upsert
- **Write**: `.write.mode("overwrite").saveAsTable("table_name")` - Overwrite table
- **Write**: `.write.mode("append").saveAsTable("table_name")` - Append to table

```python
from pyspark.sql import Window
from delta.tables import DeltaTable
from datetime import datetime

# ===== BRONZE: Raw Ingestion =====
# Read raw data with schema inference or explicit schema
bronze = (
    spark.read
    .option("multiline", "true")
    .option("mode", "PERMISSIVE")  # Allow malformed records (sent to _corrupt_record)
    .option("columnNameOfCorruptRecord", "_corrupt_record")
    .json("Files/raw/events/")
    .withColumn("_ingestion_timestamp", F.current_timestamp())
    .withColumn("_source_file", F.input_file_name())
)

# Write bronze with partitioning by ingestion date
bronze.write.partitionBy(
    F.date_format("_ingestion_timestamp", "yyyy-MM-dd").alias("_ingestion_date")
).format("delta").mode("append").save("Tables/Bronze/Events")

# ===== SILVER: Cleaned and Standardized =====
# Read from bronze (incremental: only new data)
bronze_df = spark.read.format("delta").table("Bronze.Events")
watermark_date = datetime(2025, 1, 1)  # Or read from control table

silver = (
    bronze_df
    # Filter incremental data
    .filter(F.col("_ingestion_timestamp") >= F.lit(watermark_date))
    
    # Data type conversions with error handling
    .withColumn("EventTime", 
        F.coalesce(
            F.to_timestamp("time", "yyyy-MM-dd'T'HH:mm:ss'Z'"),
            F.to_timestamp("timestamp", "yyyy-MM-dd HH:mm:ss"),
            F.to_timestamp("event_time")
        )
    )
    .withColumn("EventDate", F.to_date("EventTime"))
    
    # Standardize string fields
    .withColumn("EventType", F.upper(F.trim(F.coalesce("event_type", "eventType", "type"))))
    .withColumn("CustomerId", F.regexp_replace(F.col("customer_id"), "[^0-9]", ""))  # Clean IDs
    
    # Normalize numeric fields
    .withColumn("Amount", 
        F.when(F.col("amount").isNotNull(), 
            F.coalesce(
                F.col("amount").cast("decimal(18,2)"),
                F.regexp_replace(F.col("amount_str"), "[^0-9.]", "").cast("decimal(18,2)")
            )
        )
    )
    
    # Data quality flags
    .withColumn("_is_valid", 
        F.when(
            (F.col("EventTime").isNotNull()) & 
            (F.col("CustomerId").isNotNull()) & 
            (F.col("EventType").isNotNull()) &
            (F.col("_corrupt_record").isNull()),
            F.lit(True)
        ).otherwise(F.lit(False))
    )
    
    # Remove corrupt records
    .filter(F.col("_is_valid") == True)
    .drop("_corrupt_record")
)

# Deduplication: Keep latest record per eventId
dedupe_window = Window.partitionBy("eventId").orderBy(F.col("EventTime").desc(), F.col("_ingestion_timestamp").desc())
silver_deduped = (
    silver
    .withColumn("_row_num", F.row_number().over(dedupe_window))
    .filter(F.col("_row_num") == 1)
    .drop("_row_num")
)

# Write silver with partitioning and schema evolution
(silver_deduped
    .write
    .format("delta")
    .mode("append")
    .option("mergeSchema", "true")  # Allow schema evolution
    .partitionBy("EventDate")
    .option("delta.autoOptimize.optimizeWrite", "true")  # Auto-optimize writes
    .option("delta.autoOptimize.autoCompact", "true")
    .save("Tables/Silver/Events")
)

# ===== GOLD: Business Logic & Aggregations =====
# Read from silver
silver_df = spark.read.format("delta").table("Silver.Events")

# Gold Layer 1: Enriched facts (join with dimensions)
gold_facts = (
    silver_df
    .filter(F.col("EventDate") >= F.date_sub(F.current_date(), 90))  # Last 90 days
    .join(
        F.broadcast(spark.read.table("Silver.DimCustomer")),  # Broadcast small dimension
        on="CustomerId",
        how="left"
    )
    .join(
        spark.read.table("Silver.DimProduct"),
        on="ProductId",
        how="left"
    )
    # Add calculated fields
    .withColumn("Revenue", F.col("Amount") * F.coalesce(F.col("Quantity"), F.lit(1)))
    .withColumn("IsWeekend", F.when(F.dayofweek("EventDate").isin([1, 7]), True).otherwise(False))
    .withColumn("HourOfDay", F.hour("EventTime"))
)

# Write gold facts
(gold_facts
    .write
    .format("delta")
    .mode("overwrite")
    .option("overwriteSchema", "true")
    .partitionBy("EventDate")
    .save("Tables/Gold/FactEvents")
)

# Gold Layer 2: Aggregated metrics for BI
gold_aggregated = (
    gold_facts
    .groupBy(
        "EventDate",
        "CustomerId",
        "CustomerSegment",  # From dimension
        "ProductCategory"
    )
    .agg(
        F.count("*").alias("EventCount"),
        F.sum("Revenue").alias("TotalRevenue"),
        F.avg("Amount").alias("AvgAmount"),
        F.max("EventTime").alias("LastEventTime"),
        F.countDistinct("EventType").alias("UniqueEventTypes")
    )
    .withColumn("_load_timestamp", F.current_timestamp())
)

# Upsert pattern for aggregated table (incremental updates)
gold_table = DeltaTable.forPath(spark, "Tables/Gold/EventsDailyAgg")
(gold_table.alias("t")
    .merge(
        gold_aggregated.alias("s"),
        "t.EventDate = s.EventDate AND t.CustomerId = s.CustomerId"
    )
    .whenMatchedUpdateAll()
    .whenNotMatchedInsertAll()
    .execute())

# ===== Data Quality Reporting =====
# Generate data quality metrics
dq_metrics = (
    silver_df
    .agg(
        F.count("*").alias("total_rows"),
        F.sum(F.when(F.col("_is_valid") == False, 1).otherwise(0)).alias("invalid_rows"),
        F.countDistinct("CustomerId").alias("unique_customers"),
        F.min("EventDate").alias("min_date"),
        F.max("EventDate").alias("max_date")
    )
)

# Write DQ metrics to monitoring table
dq_metrics.withColumn("_check_timestamp", F.current_timestamp()).write.mode("append").saveAsTable("Monitoring.DataQualityEvents")
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

