# Databricks Usage Examples

Azure Databricks provides a collaborative environment to work with large-scale data and conduct advanced analytics. Here are some common examples of how you can use Databricks:

## 1. Loading Data from Azure Blob Storage:

Assuming you have already set up the connection as mentioned before, you can load data from Azure Blob Storage as follows:

```python
df = spark.read.text("wasbs://<your_container>@<your_account_name>.blob.core.windows.net/<file_path>")
```

## 2. Conduct Descriptive Analysis:

Once you have your data loaded into a DataFrame, you can conduct a quick descriptive analysis:

```python
df.describe().show()
```

## 3. Running SQL Queries:

Databricks allows you to register your DataFrames as temporary tables and then run SQL queries on them:

```python
df.createOrReplaceTempView("my_table")
results = spark.sql("SELECT * FROM my_table WHERE column1 = 'value'")
```

## 4. Using MLlib for Machine Learning:

Databricks seamlessly integrates with MLlib, Spark's machine learning library. Here's a simple example of how to train a linear regression model:

```python
from pyspark.ml.regression import LinearRegression

# Assuming 'df' is your DataFrame and you've done the necessary preprocessing
lr = LinearRegression(featuresCol='features', labelCol='label')
lr_model = lr.fit(df)
```

## 5. Data Visualization:

Databricks offers built-in visualizations for your data. After running a cell that returns a DataFrame, simply select the chart icon at the bottom of the cell to explore different visualizations.

```python
# Example: Getting the count of values in a specific column
value_counts = df.groupBy("column_name").count()
display(value_counts)  # Use the display function for richer visualizations
```

These are just some basic examples of what you can do with Azure Databricks. The service offers a wide range of tools and capabilities for large-scale data processing and analytics.