# PySpark Essential Training: Building Data Pipelines
**Course**: PySpark Essential Training: Introduction to Building Data Pipelines  
**Instructor**: Sam Bail  
**Platform**: LinkedIn Learning  
**Date**: May 15, 2026

---

## Table of Contents
1. [Course Overview](#course-overview)
2. [Setup & Installation](#setup--installation)
3. [SparkSession Basics](#sparksession-basics)
4. [Data Formats & Loading Data](#data-formats--loading-data)
5. [Basic Data Querying](#basic-data-querying)
6. [Sorting DataFrames](#sorting-dataframes)
7. [Filtering Data](#filtering-data)
8. [Handling Missing Data](#handling-missing-data)
9. [Creating & Transforming Columns](#creating--transforming-columns)
10. [Unions & Joins](#unions--joins)
11. [Aggregations](#aggregations)
12. [Writing DataFrames to Files](#writing-dataframes-to-files)
13. [PySpark SQL](#pyspark-sql)
14. [Key Takeaways](#key-takeaways)

---

## Course Overview

This course introduces PySpark, the Python API for Apache Spark, which enables processing large-scale data efficiently using distributed computing. The course focuses on building robust data pipelines by teaching data manipulation, transformation, and aggregation techniques essential for data engineering.

**Key Topics Covered:**
- Setting up PySpark environment
- Loading and exploring data
- Data transformation and cleaning
- Data aggregation and analysis
- SQL queries with PySpark
- Writing processed data to various formats

---

## Setup & Installation

### Installing PySpark

```python
# Install pyspark package
!pip install pyspark
```

### Checking PySpark Version

```python
import pyspark
pyspark.__version__
```

**Note**: Ensure you have Java installed and SPARK_HOME environment variable configured for local development.

---

## SparkSession Basics

### Creating a SparkSession

A `SparkSession` is the entry point for PySpark functionality. It represents the connection to a Spark cluster.

```python
from pyspark.sql import SparkSession

# Create or retrieve an existing SparkSession
spark = SparkSession.builder.getOrCreate()
spark
```

**Key Points:**
- `SparkSession` provides methods to read data, execute queries, and configure Spark settings
- `.getOrCreate()` returns an existing session if one exists, or creates a new one
- The session object can be used to access all Spark SQL and DataFrame functionality

---

## Data Formats & Loading Data

### Reading Parquet Files

Parquet is a columnar storage format optimized for analytical queries.

```python
# Read parquet file into a DataFrame
df = spark.read.parquet('/path/to/yellow_tripdata_2025-01.parquet')
```

### Reading CSV Files

```python
# Read CSV with header option
taxi_zone_lookup = spark.read.option('header', 'true').csv('/path/to/taxi_zone_lookup.csv')
```

**Format Comparison:**
- **Parquet**: Compressed, column-oriented, ideal for big data analytics
- **CSV**: Text-based, human-readable, widely compatible but larger file size
- **Other formats**: JSON, ORC, Delta Lake, etc.

---

## Basic Data Querying

### Viewing Data

```python
# Display first 5 rows
df.show(5)

# Display schema (column names and data types)
df.printSchema()

# Get column names as list
df.schema.names

# Get row count
df.count()

# Descriptive statistics
df.describe(['passenger_count', 'total_amount']).show()
```

### Selecting Columns

There are multiple ways to select columns from a DataFrame:

```python
# Method 1: Dot notation (returns Column object, not displayed)
df.passenger_count

# Method 2: Bracket notation (returns Column object, not displayed)
df['passenger_count']

# Method 3: Using select() method
df.select('passenger_count')

# Method 4: Using col() function with select
from pyspark.sql.functions import col
df.select(col('passenger_count')).show()

# Method 5: Select multiple columns
df.select('passenger_count', 'total_amount').show()
```

**Best Practice**: Use `col()` function for complex operations and `select()` for clarity.

---

## Sorting DataFrames

### Sorting by Single Column

```python
# Sort descending by total_amount
df.sort('total_amount', ascending=False).show()

# Sort ascending (default)
df.sort('total_amount').show()
```

### Sorting by Multiple Columns

```python
# Sort by multiple columns with different orders
df.sort(['total_amount', 'passenger_count'], 
        ascending=[False, False]).show()
```

**Key Points:**
- Default sort order is ascending
- Use `ascending` parameter to control sort direction
- Multiple columns are sorted in the order specified
- Sorting is performed on the Spark cluster, not in memory

---

## Filtering Data

### Basic Filtering

Filtering selects rows that meet specified conditions.

```python
# Filter with single condition
df.filter('Airport_fee > 0').show()

# Filter with multiple conditions (AND)
df.filter('Airport_fee > 0 and passenger_count > 2').show()
```

### Combining Filter with Select and Sort

PySpark supports method chaining for readable and efficient data pipelines:

```python
# Chain operations together
df.filter('Airport_fee = 0 and passenger_count > 2')\
  .select('VendorID', 'passenger_count', 'total_amount')\
  .sort('passenger_count', ascending=False)\
  .show()
```

**Filtering Syntax:**
- Use SQL-like WHERE clause syntax inside `filter()`
- Logical operators: `and`, `or`, `not`
- Comparison operators: `>`, `<`, `=`, `!=`, `>=`, `<=`

---

## Handling Missing Data

### Detecting NULL Values

```python
from pyspark.sql.functions import col, isnull

# Count NULL values in a column
df.filter(isnull(col('fare_amount'))).count()
```

### Filling Missing Data

```python
# Fill NULL values with a default value
df1 = df.fillna({'passenger_count': 1})

# Verify the fill worked
df1.filter(isnull(col('passenger_count'))).count()
```

**Common Strategies for Handling Missing Data:**
- **Forward fill**: Use previous value
- **Backward fill**: Use next value
- **Default value**: Replace with constant (0, 1, mean, etc.)
- **Drop rows**: Remove rows with missing values
- **Drop columns**: Remove columns with too many nulls

---

## Creating & Transforming Columns

### Adding New Columns

```python
from pyspark.sql.functions import unix_timestamp, round

# Create a new column with calculated values
df1 = df.withColumn(
    'trip_duration_minutes',
    round(
        (unix_timestamp('tpep_dropoff_datetime') - 
         unix_timestamp('tpep_pickup_datetime')) / 60,
        1
    )
)
```

### Renaming Columns

```python
# Rename multiple columns at once
df2 = df1.select('tpep_pickup_datetime', 'tpep_dropoff_datetime',
                 'trip_duration_minutes', 'PULocationID', 'DOLocationID',
                 'passenger_count', 'fare_amount', 'Airport_fee', 'total_amount')\
          .withColumnsRenamed({
              'tpep_pickup_datetime': 'pu_datetime',
              'tpep_dropoff_datetime': 'do_datetime',
              'PULocationID': 'pu_location_id',
              'DOLocationID': 'do_location_id',
              'Airport_fee': 'airport_fee'
          })
```

### Dropping Columns

```python
# Drop unwanted columns (immutable - returns new DataFrame)
df1.drop('VendorID', 'RatecodeID').show()
```

**Important**: DataFrames are immutable. All transformations return new DataFrames.

---

## Unions & Joins

### Union (Combining Rows)

Union concatenates DataFrames vertically (adds rows).

```python
# Load additional data
df_feb = spark.read.parquet('/path/to/yellow_tripdata_2025-02.parquet')

# Union two DataFrames
df_2025 = df.union(df_feb)

# Verify combined row count
df_2025.count()
```

**Union Requirements:**
- DataFrames must have the same schema
- Column order matters
- Use `unionByName()` if columns are in different order

### Join (Combining Columns)

Join combines DataFrames horizontally (adds columns) based on matching conditions.

```python
# Load lookup table
taxi_zone_lookup = spark.read.option('header', 'true')\
                            .csv('/path/to/taxi_zone_lookup.csv')

# Left join: Keep all rows from left DataFrame
df_joined = df_2025.join(
    taxi_zone_lookup,
    df_2025.PULocationID == taxi_zone_lookup.LocationID,
    'left'
)

df_joined.show()
```

**Join Types:**
- **inner**: Only matching rows
- **left**: All rows from left + matching rows from right
- **right**: All rows from right + matching rows from left
- **outer/full**: All rows from both DataFrames
- **cross**: Cartesian product (use with caution on large datasets)

---

## Aggregations

### Counting Groups

```python
# Count occurrences of each payment type
df.groupBy('payment_type').count().sort('payment_type').show()
```

**Data Dictionary (Payment Types):**
- 0 = Flex Fare trip
- 1 = Credit card payment
- 2 = Cash payment
- 3 = No charge
- 4 = Dispute
- 5 = Unknown
- 6 = Voided trip

### Calculating Averages

```python
# Method 1: Using avg() method
df.groupBy('payment_type').avg('total_amount').show()

# Method 2: Using agg() with avg() function (preferred)
from pyspark.sql.functions import avg
df.groupBy('payment_type').agg(avg('total_amount')).show()

# Method 3: With column alias for clarity
df.groupBy('payment_type')\
  .agg(avg('total_amount').alias('avg_amount'))\
  .show()
```

**Common Aggregation Functions:**
- `count()`: Count of rows
- `sum()`: Sum of values
- `avg()`: Average value
- `min()/max()`: Minimum/maximum value
- `stddev()`: Standard deviation
- `first()/last()`: First/last value

### Complex Aggregations

```python
# Multiple aggregations at once
from pyspark.sql.functions import count, avg, min, max

df.groupBy('Borough')\
  .agg(
      count('*').alias('trip_count'),
      avg('total_amount').alias('avg_amount'),
      min('total_amount').alias('min_amount'),
      max('total_amount').alias('max_amount')
  )\
  .show()
```

---

## Writing DataFrames to Files

### Writing to CSV

```python
# Create aggregated dataframe
avg_fare = df.groupBy('payment_type')\
             .agg(avg('total_amount'))\
             .sort('payment_type')

# Write to CSV
avg_fare.write.csv(
    '/path/to/output/avg_fare',
    header=True,
    mode='overwrite'
)
```

**Write Modes:**
- **overwrite**: Replace existing files (use with caution)
- **append**: Add to existing files
- **ignore**: Do nothing if files exist
- **error**: Raise error if files exist (default)

### Writing Other Formats

```python
# Write to Parquet
df.write.mode('overwrite').parquet('/path/to/output.parquet')

# Write to JSON
df.write.mode('overwrite').json('/path/to/output.json')

# Write to database (requires appropriate connector)
# df.write.mode('overwrite').jdbc(url, table, properties)
```

---

## PySpark SQL

### Creating Temporary Views

```python
# Read data
taxi = spark.read.parquet('/path/to/yellow_tripdata_2025-01.parquet')

# Create temporary view (valid only for this session)
taxi.createOrReplaceTempView('taxi')
```

### Basic SQL Queries

```python
# Simple SELECT query
spark.sql('SELECT * FROM taxi WHERE total_amount > 50').show()
```

### Chaining SQL with DataFrame Operations

```python
# Combine SQL query with DataFrame operations
spark.sql('SELECT * FROM taxi WHERE total_amount > 50')\
  .filter('passenger_count > 2')\
  .select('payment_type', 'passenger_count', 'total_amount')\
  .show()
```

### Multi-line SQL Queries

```python
# For complex queries, use triple-quoted strings
query = '''
SELECT
    payment_type,
    passenger_count,
    total_amount
FROM taxi
WHERE
    total_amount > 50
    AND
    passenger_count > 2
'''

spark.sql(query).show()
```

### SQL Joins

```python
# Create views for both tables
taxi_jan2025.createOrReplaceTempView('taxi_jan2025')
taxi_lookup.createOrReplaceTempView('taxi_lookup')

# Execute join query
query = '''
SELECT
    DOLocationID,
    Borough,
    total_amount
FROM taxi_jan2025
LEFT JOIN taxi_lookup
    ON DOLocationID = LocationID
'''

joined_df = spark.sql(query)
```

### SQL Aggregations

```python
# Group by and aggregate in SQL
query = '''
SELECT
    Borough,
    COUNT(*) as trip_count,
    AVG(total_amount) as avg_amount,
    MIN(total_amount) as min_amount,
    MAX(total_amount) as max_amount
FROM taxi_jan2025
LEFT JOIN taxi_lookup ON DOLocationID = LocationID
GROUP BY Borough
'''

spark.sql(query).show()
```

**Advantages of PySpark SQL:**
- Familiar SQL syntax for those with database background
- Catalyst optimizer optimizes execution automatically
- Can mix SQL with DataFrame operations seamlessly
- More readable for complex transformations

---

## Key Takeaways

### Essential PySpark Concepts

1. **SparkSession**: Entry point for all PySpark functionality
2. **DataFrames**: Distributed, immutable collections of data
3. **Lazy Evaluation**: Transformations are not executed until an action is called
4. **Distributed Processing**: Operations happen across cluster nodes for scalability
5. **Immutability**: All transformations return new DataFrames

### Data Pipeline Best Practices

```
Load → Filter → Transform → Aggregate → Write
  ↓        ↓         ↓          ↓         ↓
Read    Select    Calculate   Group    Output
Data    Columns   New Fields   Data     Files
```

### Performance Optimization Tips

1. **Filter early**: Apply filters as soon as possible to reduce data size
2. **Select columns**: Only select needed columns to reduce memory usage
3. **Use Parquet**: Compress and store data in columnar format
4. **Partition data**: Divide large datasets by date, region, etc.
5. **Cache strategically**: Cache intermediate results that will be reused
6. **Avoid expensive operations**: Minimize shuffles and unnecessary joins

### Common Workflows

**Workflow 1: Data Exploration**
```python
df.show()                    # Preview data
df.count()                   # Check size
df.printSchema()             # Understand structure
df.describe().show()         # Get statistics
```

**Workflow 2: Data Cleaning**
```python
df = df.fillna({'col': default})     # Handle nulls
df = df.drop('unwanted_col')         # Remove columns
df = df.filter('valid_condition')    # Filter bad data
```

**Workflow 3: Data Transformation**
```python
df = df.withColumn('new_col', ...)                 # Add columns
df = df.withColumnsRenamed({'old': 'new'})        # Rename
df = df.select('col1', 'col2')                     # Select subset
```

**Workflow 4: Data Analysis**
```python
result = df.groupBy('category')\
           .agg(avg('value').alias('avg_value'))\
           .sort('avg_value', ascending=False)
result.show()
```

---

## Conclusion

This course provided a comprehensive introduction to PySpark for building data pipelines. By mastering these fundamental concepts, you can:

- ✅ Load and explore various data formats
- ✅ Transform and clean data efficiently
- ✅ Combine datasets through unions and joins
- ✅ Aggregate data for analysis
- ✅ Write processed data to files
- ✅ Execute SQL queries on distributed data
- ✅ Build scalable, production-ready data pipelines

**Next Steps:**
- Practice with larger datasets
- Explore advanced transformations and window functions
- Learn about performance tuning and optimization
- Study Spark streaming for real-time data processing
- Implement production pipelines with error handling and logging

---

**Last Updated**: May 15, 2026  
**Course Link**: LinkedIn Learning - PySpark Essential Training
