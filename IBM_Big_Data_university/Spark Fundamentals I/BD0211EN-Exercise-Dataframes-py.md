
<a href="https://cognitiveclass.ai"><img src = "https://ibm.box.com/shared/static/9gegpsmnsoo25ikkbl4qzlvlyjbgxs5x.png" width = 400> </a>

<h1 align = "center"> Spark Fundamentals I - Introduction to Spark</h1>
<h2 align = "center"> Python - Working with Dataframes</h2>
<br align = "left">

**Related free online courses:**

Related courses can be found in the following learning paths:

- [Spark Fundamentals path](http://cocl.us/Spark_Fundamentals_Path)
- [Big Data Fundamentals path](http://cocl.us/Big_Data_Fundamentals_Path)

<img src = "http://spark.apache.org/images/spark-logo.png", height = 100, align = 'left'>

A DataFrame is two-dimensional. Columns can be of different data types. DataFrames accept many data inputs including series and other DataFrames. You can pass indexes (row labels) and columns (column labels). Indexes can be numbers, dates, or strings/tuples.

Pandas is a library used for data manipulation and analysis. Pandas offers data structures and operations for creating and manipulating Data Series and DataFrame objects. Data can be imported from various data sources, e.g., Numpy arrays, Python dictionaries and CSV files. Pandas allows you to manipulate, organize and display the data. 

In this short notebook, we will load and explore the mtcars dataset. Specifically, this tutorial covers:

1. Loading data in memory
1. Creating SQLContext
1. Creating Spark DataFrame
1. Group data by columns 
1. Operating on columns
1. Running SQL Queries from a Spark DataFrame


## Loading in a DataFrame
To create a Spark DataFrame we load an external DataFrame, called `mtcars`. This DataFrame includes 32 observations on 11 variables:

[, 1]	mpg	Miles/(US) --> gallon  
[, 2]	cyl	--> Number of cylinders  
[, 3]	disp	--> Displacement (cu.in.)  
[, 4]	hp -->	Gross horsepower  
[, 5]	drat -->	Rear axle ratio  
[, 6]	wt -->	Weight (lb/1000)  
[, 7]	qsec -->	1/4 mile time  
[, 8]	vs -->	V/S  
[, 9]	am -->	Transmission (0 = automatic, 1 = manual)  
[,10]	gear -->	Number of forward gears  
[,11]	carb -->	Number of carburetors  




```python
import pandas as pd
mtcars = pd.read_csv('https://ibm.box.com/shared/static/f1dhhjnzjwxmy2c1ys2whvrgz05d1pui.csv')
```


```python
mtcars.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>car</th>
      <th>mpg</th>
      <th>cyl</th>
      <th>disp</th>
      <th>hp</th>
      <th>drat</th>
      <th>wt</th>
      <th>qsec</th>
      <th>vs</th>
      <th>am</th>
      <th>gear</th>
      <th>carb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Mazda RX4</td>
      <td>21.0</td>
      <td>6</td>
      <td>160.0</td>
      <td>110</td>
      <td>3.90</td>
      <td>2.620</td>
      <td>16.46</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Mazda RX4 Wag</td>
      <td>21.0</td>
      <td>6</td>
      <td>160.0</td>
      <td>110</td>
      <td>3.90</td>
      <td>2.875</td>
      <td>17.02</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Datsun 710</td>
      <td>22.8</td>
      <td>4</td>
      <td>108.0</td>
      <td>93</td>
      <td>3.85</td>
      <td>2.320</td>
      <td>18.61</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hornet 4 Drive</td>
      <td>21.4</td>
      <td>6</td>
      <td>258.0</td>
      <td>110</td>
      <td>3.08</td>
      <td>3.215</td>
      <td>19.44</td>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Hornet Sportabout</td>
      <td>18.7</td>
      <td>8</td>
      <td>360.0</td>
      <td>175</td>
      <td>3.15</td>
      <td>3.440</td>
      <td>17.02</td>
      <td>0</td>
      <td>0</td>
      <td>3</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



## Initialize SQLContext
To work with dataframes we need an SQLContext which is created using `SQLContext(sc)`. SQLContext uses SparkContext which has been already created in Data Scientist Workbench, named `sc`. 


```python
sqlContext = SQLContext(sc)
```

## Creating Spark DataFrames
With SQLContext and a loaded local DataFrame, we create a Spark DataFrame:


```python
sdf = sqlContext.createDataFrame(mtcars) 
sdf.printSchema()
```

    root
     |-- car: string (nullable = true)
     |-- mpg: double (nullable = true)
     |-- cyl: long (nullable = true)
     |-- disp: double (nullable = true)
     |-- hp: long (nullable = true)
     |-- drat: double (nullable = true)
     |-- wt: double (nullable = true)
     |-- qsec: double (nullable = true)
     |-- vs: long (nullable = true)
     |-- am: long (nullable = true)
     |-- gear: long (nullable = true)
     |-- carb: long (nullable = true)
    


## Displays the content of the DataFrame 



```python
sdf.show(5)
```

    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
    |              car| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
    |        Mazda RX4|21.0|  6|160.0|110| 3.9| 2.62|16.46|  0|  1|   4|   4|
    |    Mazda RX4 Wag|21.0|  6|160.0|110| 3.9|2.875|17.02|  0|  1|   4|   4|
    |       Datsun 710|22.8|  4|108.0| 93|3.85| 2.32|18.61|  1|  1|   4|   1|
    |   Hornet 4 Drive|21.4|  6|258.0|110|3.08|3.215|19.44|  1|  0|   3|   1|
    |Hornet Sportabout|18.7|  8|360.0|175|3.15| 3.44|17.02|  0|  0|   3|   2|
    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
    only showing top 5 rows
    


## Selecting columns


```python
sdf.select('mpg').show(5)
```

    +----+
    | mpg|
    +----+
    |21.0|
    |21.0|
    |22.8|
    |21.4|
    |18.7|
    +----+
    only showing top 5 rows
    


## Filtering Data
Filter the DataFrame to only retain rows with `mpg` less than 18


```python
sdf.filter(sdf['mpg'] < 18).show(5)
```

    +-----------+----+---+-----+---+----+----+-----+---+---+----+----+
    |        car| mpg|cyl| disp| hp|drat|  wt| qsec| vs| am|gear|carb|
    +-----------+----+---+-----+---+----+----+-----+---+---+----+----+
    | Duster 360|14.3|  8|360.0|245|3.21|3.57|15.84|  0|  0|   3|   4|
    |  Merc 280C|17.8|  6|167.6|123|3.92|3.44| 18.9|  1|  0|   4|   4|
    | Merc 450SE|16.4|  8|275.8|180|3.07|4.07| 17.4|  0|  0|   3|   3|
    | Merc 450SL|17.3|  8|275.8|180|3.07|3.73| 17.6|  0|  0|   3|   3|
    |Merc 450SLC|15.2|  8|275.8|180|3.07|3.78| 18.0|  0|  0|   3|   3|
    +-----------+----+---+-----+---+----+----+-----+---+---+----+----+
    only showing top 5 rows
    


## Operating on Columns
SparkR also provides a number of functions that can be directly applied to columns for data processing and aggregation. The example below shows the use of basic arithmetic functions to convert lb to metric ton.


```python
sdf.withColumn('wtTon', sdf['wt'] * 0.45).show(6)
```

    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+-------+
    |              car| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|  wtTon|
    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+-------+
    |        Mazda RX4|21.0|  6|160.0|110| 3.9| 2.62|16.46|  0|  1|   4|   4|  1.179|
    |    Mazda RX4 Wag|21.0|  6|160.0|110| 3.9|2.875|17.02|  0|  1|   4|   4|1.29375|
    |       Datsun 710|22.8|  4|108.0| 93|3.85| 2.32|18.61|  1|  1|   4|   1|  1.044|
    |   Hornet 4 Drive|21.4|  6|258.0|110|3.08|3.215|19.44|  1|  0|   3|   1|1.44675|
    |Hornet Sportabout|18.7|  8|360.0|175|3.15| 3.44|17.02|  0|  0|   3|   2|  1.548|
    |          Valiant|18.1|  6|225.0|105|2.76| 3.46|20.22|  1|  0|   3|   1|  1.557|
    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+-------+
    only showing top 6 rows
    



```python
sdf.show(6)
```

    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
    |              car| mpg|cyl| disp| hp|drat|   wt| qsec| vs| am|gear|carb|
    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
    |        Mazda RX4|21.0|  6|160.0|110| 3.9| 2.62|16.46|  0|  1|   4|   4|
    |    Mazda RX4 Wag|21.0|  6|160.0|110| 3.9|2.875|17.02|  0|  1|   4|   4|
    |       Datsun 710|22.8|  4|108.0| 93|3.85| 2.32|18.61|  1|  1|   4|   1|
    |   Hornet 4 Drive|21.4|  6|258.0|110|3.08|3.215|19.44|  1|  0|   3|   1|
    |Hornet Sportabout|18.7|  8|360.0|175|3.15| 3.44|17.02|  0|  0|   3|   2|
    |          Valiant|18.1|  6|225.0|105|2.76| 3.46|20.22|  1|  0|   3|   1|
    +-----------------+----+---+-----+---+----+-----+-----+---+---+----+----+
    only showing top 6 rows
    


## Grouping, Aggregation
Spark DataFrames support a number of commonly used functions to aggregate data after grouping. For example we can compute the average weight of cars by their cylinders as shown below:


```python
sdf.groupby(['cyl'])\
.agg({"wt": "AVG"})\
.show(5)
```

    +---+-----------------+
    |cyl|          avg(wt)|
    +---+-----------------+
    |  4|2.285727272727273|
    |  6|3.117142857142857|
    |  8|3.999214285714286|
    +---+-----------------+
    



```python
# We can also sort the output from the aggregation to get the most common cars
car_counts = sdf.groupby(['cyl'])\
.agg({"wt": "count"})\
.sort("count(wt)", ascending=False)\
.show(5)
```

    +---+---------+
    |cyl|count(wt)|
    +---+---------+
    |  8|       14|
    |  4|       11|
    |  6|        7|
    +---+---------+
    


### Running SQL Queries from Spark DataFrames
A Spark DataFrame can also be registered as a temporary table in Spark SQL and registering a DataFrame as a table allows you to run SQL queries over its data. The `sql` function enables applications to run SQL queries programmatically and returns the result as a DataFrame.




```python
# Register this DataFrame as a table.
sdf.registerTempTable("cars")

# SQL statements can be run by using the sql method
highgearcars = sqlContext.sql("SELECT gear FROM cars WHERE cyl >= 4 AND cyl <= 9")
highgearcars.show(6)   
```

    +----+
    |gear|
    +----+
    |   4|
    |   4|
    |   4|
    |   3|
    |   3|
    |   3|
    +----+
    only showing top 6 rows
    


NOTE: This tutorial draws heavily from the original 
[Spark Quick Start Guide](http://spark.apache.org/docs/latest/quick-start.html)

<div class="alert alert-success alertsuccess" style="margin-top: 20px">
**Tip**: Enjoyed using Jupyter notebooks with Spark? Get yourself a free 
    <a href="http://cocl.us/DSX_on_Cloud">IBM Cloud</a> account where you can use Data Science Experience notebooks
    and have *two* Spark executors for free!
</div>

### Summary
Having completed this exercise, you should now be able to load data in memory, create SQLContext, create Spark DataFrame, group data by columns, and run SQL Queries from a Spark dataframe.

This notebook is part of the free course on **Cognitive Class** called *Spark Fundamentals I*. If you accessed this notebook outside the course, you can take this free self-paced course, online by going to: http://cocl.us/Spark_Fundamentals_I

### About the Authors:  
Hi! It's [Alex Aklson](https://www.linkedin.com/in/aklson/), one of the authors of this notebook. I hope you found this lab educational! There is much more to learn about Spark but you are well on your way. Feel free to connect with me if you have any questions.
<hr>
