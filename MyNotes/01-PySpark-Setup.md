# PySpark Setup


# NYC Taxi Dataset

- The NYC Taxi Dataset is a popular public dataset that contains information about taxi trips in New York City. It includes details such as pickup and dropoff locations, timestamps, trip distances, and fare amounts.

URL - https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page

1. https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2026-01.parquet
2. https://d37ci6vzurychx.cloudfront.net/trip-data/yellow_tripdata_2026-02.parquet


## Local Setup

- To set up PySpark on your local machine, you need to follow these steps:

1. Install Java: Spark requires Java to run, so you need to have Java installed on your machine. You can download and install Java from the official website: https://www.oracle.com/java/technologies/javase-downloads.html

2. Install Spark: You can download and install Spark from the official Apache Spark website: https://spark.apache.org/downloads.html. Make sure to choose the version that is compatible with your Java installation.

3. Set Environment Variables: After installing Java and Spark, you need to set the environment variables for Java and Spark. This involves adding the paths to the Java and Spark binaries to your system's PATH variable.

- Once you have completed these steps, you can verify your PySpark installation by opening a terminal and running the following command:

```bash pyspark
```

- Create Venv and Install PySpark

```bash 
python -m venv pyspark-env
source pyspark-env/bin/activate     
 # On Windows, use 

```pyspark-env\Scripts\activate`
pip install pyspark
```
- ipython kernel for Jupyter Notebook

**Note**: You need to install `ipykernel` first before installing the kernel spec.

```bash
pip install ipykernel
ipython kernel install --user --name pyspark-env
```

Or as a single command:

```bash
pip install ipykernel && ipython kernel install --user --name pyspark-env
```

**Successful Installation Result**:
```
Installed kernelspec pyspark-env in C:\Users\satis\AppData\Roaming\jupyter\kernels\pyspark-env
``` 

- Run the following command to check if the kernel is installed successfully:

```bash 
jupyter kernelspec list
```

- Install Jupyter Notebook package:

```bash
pip install notebook
```

- Verify the notebook installation:

```bash
jupyter notebook --version
```

- Mount training-data from local drive to Jupyter Notebook in ipython kernel:

```bash 
jupyter notebook --notebook-dir=path/to/training-data 
```

**Example with actual path**:
```bash
jupyter notebook --notebook-dir=d:\2026-Courses\Elearning-AI-ML-DS\pyspark\LL-pyspark-essential-training\pyspark-essential-training-build-robust-data-pipelines-2737073\training-data
```
** Eample to open ipython kernel in the same directory as training-data**:
```bash 
cd d:\2026-Courses\Elearning-AI-ML-DS\pyspark\LL-pyspark-essential-training\pyspark-essential-training-build-robust-data-pipelines-2737073\training-data
jupyter notebook    --notebook-dir=. 
```

```python
import os
import sys
os.environ['PYSPARK_PYTHON'] = sys.executable
os.environ['PYSPARK_DRIVER_PYTHON'] = sys.executable
```

---




## Google Colab

- Google Colab is a free cloud-based Jupyter notebook environment that allows you to run Python code in the browser. It provides access to powerful computing resources, including GPUs and TPUs, making it an excellent choice for data science and machine learning tasks.

- Create a new notebook in Google Colab and follow the instructions below to set up PySpark.

- To use PySpark in Google Colab, you can install the necessary libraries and set up the Spark environment using the following code:

```python
!pip install pyspark
```

- After installing PySpark, you can create a SparkSession to start working with Spark:

```python
from pyspark.sql import SparkSession
```
spark = SparkSession.builder.appName("PySpark Setup").getOrCreate()
```

- Now you can use the `spark` object to interact with Spark and perform various data processing tasks.

--------




