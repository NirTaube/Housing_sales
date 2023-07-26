# Housing_sales
markdown
Copy code
# PySpark and Parquet Data - README

This README provides step-by-step instructions on how to work with Spark and Parquet data in PySpark.

## 1. Read Data from AWS S3 Bucket into a DataFrame

```python
from pyspark import SparkFiles

url = "https://2u-data-curriculum-team.s3.amazonaws.com/dataviz-classroom/v1.2/22-big-data/home_sales_revised.csv"
df = spark.read.csv(url, header=True, inferSchema=True)
df.show()
```

## 2. Create a Temporary View of the DataFrame
```python

df.createOrReplaceTempView("home_sales")
Calculate Average Price for a Four-Bedroom House Sold in Each Year
```

```python
query = """
SELECT year(date) AS year_sold, ROUND(AVG(price), 2) AS avg_price
FROM home_sales
WHERE bedrooms = 4
GROUP BY year_sold
ORDER BY year_sold
"""
result = spark.sql(query)
result.show()
```

## 4. Calculate Average Price of a Home for Each Year Built with 3 Bedrooms and 3 Bathrooms
```python
query = """
SELECT date_built, ROUND(AVG(price), 2) AS avg_price
FROM home_sales
WHERE bedrooms = 3 AND bathrooms = 3
GROUP BY date_built
ORDER BY date_built
"""
result = spark.sql(query)
result.show()
```


## 5. Calculate Average Price of a Home for Each Year Built with 3 Bedrooms, 3 Bathrooms, 2 Floors, and >= 2,000 Sqft
```python
query = """
SELECT date_built, ROUND(AVG(price), 2) AS avg_price
FROM home_sales
WHERE bedrooms = 3 AND bathrooms = 3 AND floors = 2 AND sqft_living >= 2000
GROUP BY date_built
ORDER BY date_built
"""
result = spark.sql(query)
result.show()
```

## 6. Calculate "View" Rating for the Average Price of a Home with Price >= $350,000
```python

query = """
SELECT ROUND(AVG(view), 2) AS avg_view
FROM home_sales
WHERE price >= 350000
"""
result = spark.sql(query)
result.show()
```

## 7. Cache the DataFrame to Improve Query Performance
```python
spark.catalog.cacheTable("home_sales")
```

## 8. Execute Cached and Uncached Queries to Compare Performance
## 9. Partition the Parquet Data by the "date_built" Field
```python
df.write.partitionBy("date_built").parquet("partitioned_home_sales.parquet")
```
## 10. Read the Partitioned Parquet Data and Create a Temporary Table
```python

df_partitioned = spark.read.parquet("partitioned_home_sales.parquet")
df_partitioned.createOrReplaceTempView("parquet_home_sales")
```

## 11. Query the Partitioned Parquet Data
```python

query_parquet = """
SELECT ROUND(AVG(view), 2) AS avg_view
FROM parquet_home_sales
WHERE price >= 350000
"""
result_parquet = spark.sql(query_parquet)
result_parquet.show()
```

##12. Uncache the Temporary Table
```python
spark.catalog.uncacheTable("home_sales")
# or
df.unpersist()
```
By following these steps, you can efficiently work with Spark and Parquet data in PySpark, perform data analysis, and gain insights from large-scale datasets. The caching and partitioning techniques can significantly improve query performance, making it easier to process big data efficiently.

---
#Analysis

The average price for a four bedroom house sold in each year rounded to two decimal places.
```
+----+---------+
|year|avg_price|
+----+---------+
|2019| 300263.7|
|2020|298353.78|
|2021|301819.44|
|2022|296363.88|
+----+---------+
```
The average price of a home for each year the home was built that have 3 bedrooms and 3 bathrooms rounded to two decimal places
```
+----+---------+
|year|avg_price|
+----+---------+
|2010|292859.62|
|2011|291117.47|
|2012|293683.19|
|2013|295962.27|
|2014|290852.27|
|2015| 288770.3|
|2016|290555.07|
|2017|292676.79|
+----+---------+
```
 The average price of a home for each year built that have 3 bedrooms, 3 bathrooms, with two floors,
and are greater than or equal to 2,000 square feet rounded to two decimal places?
```
+----+---------+
|year|avg_price|
+----+---------+
|2010|285010.22|
|2011|276553.81|
|2012|307539.97|
|2013|303676.79|
|2014|298264.72|
|2015|297609.97|
|2016| 293965.1|
|2017|280317.58|
+----+---------+
```
The "view" rating for the average price of a home, rounded to two decimal places, where the homes are greater than
or equal to $350,000? Although this is a small dataset, determine the run time for this query.
```
+---------+--------+
|avg_price|avg_view|
+---------+--------+
|473796.26|   32.26|
+---------+--------+
```

Using the cached data, run the query that filters out the view ratings with average price 
greater than or equal to $350,000. Determine the runtime and compare it to uncached runtime
```
Uncached Result:
+--------+
|avg_view|
+--------+
|   32.26|
+--------+
```
Uncached Query Runtime: 0.025943756103515625 seconds
```
Cached Result:
+--------+
|avg_view|
+--------+
|   32.26|
+--------+
```
Cached Query Runtime: 0.21535825729370117 seconds

