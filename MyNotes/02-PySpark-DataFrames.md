# Working with DataFrames in PySpark

## What is a PySpark DataFrame?

- A PySpark DataFrame is a distributed collection of data organized into named columns, similar to a table in a relational database. It is built on top of the Resilient Distributed Dataset (RDD) and provides a higher-level API for working with structured data.

- DataFrames in PySpark are designed to handle large volumes of data across a cluster of machines, allowing for efficient processing and analysis.

- PySpark DataFrames support various data sources, including HDFS, Apache Cassandra, Apache HBase, and Amazon S3, making it easy to work with data stored in different formats and locations.

Difference between PySpark DataFrame and Pandas DataFrame is as below in table format:
| Feature | PySpark DataFrame | Pandas DataFrame |
| --- | --- | --- |
| Distribution | Distributed across a cluster | Local memory |
| Data Size | Can handle large datasets | Limited by available memory |
| Performance | Efficient for big data processing | Good for small to medium datasets |
| Use Case | Big data analytics, distributed computing | Data analysis, prototyping |
| API | High-level API for structured data | High-level API for data manipulation |
| Language Support | Python, Java, Scala, R | Python |
| mutability | Immutable | Mutable |
| loading data | Lazy loading | Eager loading |
| execution | Lazy evaluation | Immediate execution |
| fault tolerance | Built-in fault tolerance | No built-in fault tolerance |
|DAG | Directed Acyclic Graph for execution | No DAG, immediate execution |


## What Kinds of Data fomats Can We Store in a PySpark DataFrame?

- Various data sources and formats can be stored in a PySpark DataFrame, including:
  - CSV (Comma-Separated Values)
  - JSON (JavaScript Object Notation)
  - Parquet (Columnar storage format)
  - ORC (Optimized Row Columnar format)
  - Avro (Row-based storage format)
  - Text files
  - JDBC (Java Database Connectivity) for relational databases
  - HDFS (Hadoop Distributed File System) for distributed storage
  - Amazon S3 for cloud storage



## How to Create a PySpark DataFrame?

- You can create a PySpark DataFrame using various methods, including:
  - Reading data from external sources (e.g., CSV, JSON, Parquet)
  - Creating a DataFrame from a list of tuples or a Pandas DataFrame
  - Using SQL queries on existing DataFrames
- Example of creating a DataFrame from a CSV file:
```python
from pyspark.sql import SparkSession

# Create a SparkSession
spark = SparkSession.builder.appName("PySpark DataFrame Example").getOrCreate()

# Read a CSV file into a DataFrame
df = spark.read.option("header", "true").csv("path/to/your/file.csv")
```

- Example of creating a DataFrame from a list of tuples:
```python   

```

- From local data:
```python   
data = [("Alice", 30), ("Bob", 25), ("Charlie", 35)]
columns = ["Name", "Age"]
df = spark.createDataFrame(data, columns)   
```

- Example to read data from a Parquet file:
```python
df = spark.read.parquet("path/to/your/file.parquet")
df.show()
``` 

- Example of creating a DataFrame from a Pandas DataFrame:
```python
import pandas as pd

# Create a Pandas DataFrame
pdf = pd.DataFrame({"Name": ["Alice", "Bob", "Charlie"], "Age": [30, 25, 35]})

# Convert Pandas DataFrame to PySpark DataFrame
df = spark.createDataFrame(pdf)
```

## How to explore schema and data in a PySpark DataFrame?

- To explore the schema of a PySpark DataFrame, you can use the `printSchema()` method, which provides a tree-like structure of the DataFrame's columns and their data types.

 For example:
```python
df.printSchema()
```

- To explore the data in a PySpark DataFrame, you can use the `show()` method, which displays a sample of the data in a tabular format. By default, it shows the first 20 rows, but you can specify the number of rows to display.
For example:

```python
df.show(5)
```

- You can also use the `head()` method to retrieve the first few rows of the DataFrame as a list of Row objects.

 For example:
```python   
df.head(5)
```

- To get a summary of the DataFrame, including the count, mean, standard deviation, minimum, and maximum values for numeric columns, you can use the `describe()` method. For example:

```python   
df.describe().show()
```

- To get the column names of a DataFrame, you can use the `columns` attribute. For example:

```python   
df.columns
```

- df.shema.names to get column names in a list format.df.schema.names

```python
df.schema.names
```

- code with all the methods to explore schema and data in a PySpark DataFrame:

```python   
# Print the schema of the DataFrame
df.printSchema()

# Display the first few rows of the DataFrame
df.show(5)

# Get the first few rows as a list of Row objects
df.head(5)

# Get a summary of the DataFrame
df.describe().show()
# Get the column names of the DataFrame
df.columns
# Get the column names in a list format
df.schema.names
```


## How to perform basic data manipulation and transformation on a PySpark DataFrame?



## basic queries on a PySpark DataFrame

- To perform basic data manipulation and transformation on a PySpark DataFrame, you can use various methods provided by the DataFrame API. Here are some common operations:
- Filtering rows based on a condition:

```python   
filtered_df = df.filter(df["Age"] > 30)
```

- Selecting specific columns:

```python
df_with_new_column = df.withColumn("Age_in_5_years", df["Age"] + 5)
```

- selecting specific columns:

```python
selected_columns = df.select("Name", "Age")
```
