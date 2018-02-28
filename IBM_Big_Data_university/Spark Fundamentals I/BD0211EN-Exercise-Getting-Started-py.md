
<a href="https://cognitiveclass.ai"><img src = "https://ibm.box.com/shared/static/9gegpsmnsoo25ikkbl4qzlvlyjbgxs5x.png" width = 400> </a>

<h1 align = "center"> Spark Fundamentals I - Introduction to Spark</h1>
<h2 align = "center"> Getting Started</h2>
<br align = "left">

**Related free online courses:**

Related courses can be found in the following learning paths:

- [Spark Fundamentals path](http://cocl.us/Spark_Fundamentals_Path)
- [Big Data Fundamentals path](http://cocl.us/Big_Data_Fundamentals_Path) 

<img src = "http://spark.apache.org/images/spark-logo.png", height = 100, align = 'left'>

 ## Spark is built around speed and the ease of use. In these labs you will see for yourself how easy it is to get started using Spark. 

Spark’s primary abstraction is a distributed collection of items called a Resilient Distributed Dataset or RDD. In a subsequent lab exercise, you will learn more about the details of RDD. RDDs have actions, which return values, and transformations, which return pointers to new RDD.

This set of labs uses Cognitive Class Labs (formerly known as BDU Labs) to provide an interactive environment to develop applications and analyze data. It is available in either Scala or Python shells. Scala runs on the Java VM and is thus a good way to use existing Java libraries. In this lab exercise, we will set up our environment in preparation for the later labs.

After completing this set of hands-on labs, you should be able to:

1. Perform basic RDD actions and transformations
2. Use caching to speed up repeated operations


### Using this notebook

This is an interactive environment where you can show your code through cells, and documentation through markdown.

Look at the top right corner. Do you see "Python 3"? This indicates that you are running Python in this notebook.

**To run a cell:** Shift + Enter

### Try creating a new cell below.

**To create a new cell:** In the menu, go to _"Insert" > "Insert Cell Below"_. Or, click outside of a cell, and press "a" (insert cell above) or "b" (insert cell below).


```python

```

# Lab Setup

Run the following cells to get the lab data.


```python
# download the data from the IBM server
# this may take ~30 seconds depending on your internet speed
!wget --quiet https://ibm.box.com/shared/static/j8skrriqeqw66f51iyz911zyqai64j2g.zip
print("Data Downloaded!")
```

    Data Downloaded!


Let's unzip the data that we just downloaded into a directory dedicated for this course. Let's choose the directory **/resources/jupyter/labs/BD0211EN/**.


```python
# this may take ~30 seconds depending on your internet speed
!unzip -q -o -d /resources/jupyter/labs/BD0211EN/ j8skrriqeqw66f51iyz911zyqai64j2g.zip
print("Data Extracted!")
```

    Data Extracted!


The data is in a folder called **LabData**. Let's list all the files in the data that we just downloaded and extracted.


```python
# list the extracted files
!ls -1 /resources/jupyter/labs/BD0211EN/LabData
```

    followers.txt
    notebook.log
    nyctaxi100.csv
    nyctaxi.csv
    nyctaxisub.csv
    nycweather.csv
    pom.xml
    README.md
    taxistreams.py
    users.txt


Should have:
    
* followers.txt
* notebook.log
* nyctaxi100.csv
* nyctaxi.csv
* nyctaxisub.csv
* nycweather.csv
* pom.xml
* README.md
* taxistreams.py
* users.txt

### Starting with Spark

The notebooks provide code assist. For example, type in "sc." followed by the Tab key to get the list of options associated with the spark context:


```python
sc.addFile
```




    <bound method SparkContext.addFile of <pyspark.context.SparkContext object at 0x7fdb343cfdd8>>



To run a command as code, simple select the cell you want to run and either:

* Click the play button in the toolbar above
* Press "_Shift+Enter_"

Let's run a basic command and check the version of Spark running:


```python
sc.version
```




    '1.6.0'



Add in the path to the *README.md* file in **LabData**.


```python
readme = sc.textFile("/resources/jupyter/labs/BD0211EN/LabData/README.md")
```

Let’s perform some RDD actions on this text file. Count the number of items in the RDD using this command:


```python
readme.count()
```




    98



You should see that this RDD action returned a value of 103.

Let’s run another action. Run this command to find the first item in the RDD:


```python
readme.first()
```




    '# Apache Spark'



Now let’s try a transformation. Use the filter transformation to return a new RDD with a subset of the items in the file. Type in this command:


```python
linesWithSpark = readme.filter(lambda line: "Spark" in line)
```

You can even chain together transformations and actions. To find out how many lines contains the word “Spark”, type in:


```python
linesWithSpark = readme.filter(lambda line: "Spark" in line)
readme.filter(lambda line: "Spark" in line).count()
```




    18



# More on RDD Operations

This section builds upon the previous section. In this section, you will see that RDD can be used for more complex computations. You will find the line from that "README.md" file with the most words in it.

Run the following cell.


```python
readme.map(lambda line: len(line.split())).reduce(lambda a, b: a if (a > b) else b)
```




    14



There are two parts to this. The first maps a line to an integer value, the number of words in that line. In the second part reduce is called to find the line with the most words in it. The arguments to map and reduce are Python anonymous functions (lambdas), but you can use any top level Python functions. In the next step, you’ll define a max function to illustrate this feature.

Define the max function. You will need to type this in:


```python
def max(a, b):
 if a > b:
    return a
 else:
    return b
```

Now run the following with the max function:


```python
readme.map(lambda line: len(line.split())).reduce(max)
```




    14



Spark has a MapReduce data flow pattern. We can use this to do a word count on the readme file.


```python
wordCounts = readme.flatMap(lambda line: line.split()).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a+b)
```

Here we combined the flatMap, map, and the reduceByKey functions to do a word count of each word in the readme file.

To collect the word counts, use the _collect_ action.

#### It should be noted that the collect function brings all of the data into the driver node. For a small dataset, this is acceptable but, for a large dataset this can cause an Out Of Memory error. It is recommended to use collect() for testing only. The safer approach is to use the take() function e.g. print take(n)


```python
wordCounts.collect()
```




    [('guide,', 1),
     ('APIs', 1),
     ('optimized', 1),
     ('name', 1),
     ('Scala,', 1),
     ('package.', 1),
     ('particular', 3),
     ('tools', 1),
     ('must', 1),
     ('URL,', 1),
     ('params', 1),
     ('programs', 2),
     ('changed', 1),
     ('[Configuration', 1),
     ('following', 2),
     ('start', 1),
     ('computation', 1),
     ('Please', 3),
     ('SQL', 2),
     ('Spark.', 1),
     ('Hadoop,', 2),
     ('configure', 1),
     ('distributions.', 1),
     ('It', 2),
     ('run:', 1),
     ('Hive', 2),
     ('graph', 1),
     ('distribution', 1),
     ('Testing', 1),
     ('using:', 1),
     ('Building', 1),
     ('locally', 2),
     ('built,', 1),
     ('./bin/run-example', 2),
     ('have', 1),
     ('The', 1),
     ('"local"', 1),
     ('module,', 1),
     ('programming', 1),
     ('provides', 1),
     ('systems.', 1),
     ('scala>', 1),
     ('latest', 1),
     ('instance:', 1),
     ('machine', 1),
     ('online', 1),
     ('in', 5),
     ('against', 1),
     ('downloaded', 1),
     ('Alternatively,', 1),
     ('several', 1),
     ('project', 1),
     ('[params]`.', 1),
     ('engine', 1),
     ('[project', 2),
     ('supports', 2),
     ('uses', 1),
     ('1000:', 2),
     ('About', 1),
     ('individual', 1),
     ('version', 1),
     ('example:', 1),
     ('README', 1),
     ('Once', 1),
     ('help', 1),
     ('are', 1),
     ('learning,', 1),
     ('Spark', 14),
     ('instructions.', 1),
     ('[Apache', 1),
     ('GraphX', 1),
     ('analysis.', 1),
     ('Shell', 2),
     ('MLlib', 1),
     ('#', 1),
     ('Python', 2),
     ('abbreviated', 1),
     ('Try', 1),
     ('usage', 1),
     ('Documentation', 1),
     ('HDFS', 1),
     ('them,', 1),
     ('of', 5),
     ('only', 1),
     ('high-level', 1),
     ('YARN,', 1),
     ('Guide](http://spark.apache.org/docs/latest/configuration.html)', 1),
     ('N', 1),
     ('this', 1),
     ('["Third', 1),
     ('Hadoop-supported', 1),
     ('`./bin/run-example', 1),
     ('Because', 1),
     ('Spark](#building-spark).', 1),
     ('tests', 2),
     ('when', 1),
     ('basic', 1),
     ('do', 2),
     ('spark://', 1),
     ('way', 1),
     ('an', 3),
     ('different', 1),
     ('library', 1),
     ('Apache', 1),
     ('1000).count()', 1),
     ('distribution.', 1),
     ('class', 2),
     ('variable', 1),
     ('Note', 1),
     ('examples', 2),
     ('Spark"](http://spark.apache.org/docs/latest/building-spark.html).', 1),
     ('Java,', 1),
     ('use', 3),
     ('refer', 2),
     ('storage', 1),
     ('./dev/run-tests', 1),
     ('site,', 1),
     ('Example', 1),
     ('guidance', 3),
     ('set', 2),
     ('other', 1),
     ('at', 2),
     ('documentation', 3),
     ('Interactive', 2),
     ('processing,', 1),
     ('no', 1),
     ('run', 7),
     ('is', 6),
     ('And', 1),
     ('Thriftserver', 1),
     ('versions', 1),
     ('using', 2),
     ('print', 1),
     ('overview', 1),
     ('SparkPi', 2),
     ('wiki](https://cwiki.apache.org/confluence/display/SPARK).', 1),
     ('["Specifying', 1),
     ('Version"](http://spark.apache.org/docs/latest/building-spark.html#specifying-the-hadoop-version)',
      1),
     ('mesos://', 1),
     ('for', 12),
     ('its', 1),
     ('or', 3),
     ('detailed', 2),
     ('shell:', 2),
     ('the', 21),
     ('find', 1),
     ('Running', 1),
     ('sc.parallelize(1', 1),
     ('"local[N]"', 1),
     ('Pi', 1),
     ('computing', 1),
     ('DataFrames,', 1),
     ('return', 2),
     ('see', 1),
     ('See', 1),
     ('Python,', 2),
     ('You', 3),
     ('core', 1),
     ('tests](https://cwiki.apache.org/confluence/display/SPARK/Useful+Developer+Tools).',
      1),
     ('Versions', 1),
     ('rich', 1),
     ('sc.parallelize(range(1000)).count()', 1),
     ('you', 4),
     ('processing.', 1),
     ('package', 1),
     ('how', 2),
     ('MASTER', 1),
     ('works', 1),
     ('Programs', 1),
     ('one', 2),
     ('and', 10),
     ('application', 1),
     ('Distributions"](http://spark.apache.org/docs/latest/hadoop-third-party-distributions.html)',
      1),
     ('higher-level', 1),
     ('threads.', 1),
     ('Many', 1),
     ('`examples`', 2),
     ('Online', 1),
     ('a', 10),
     ('file', 1),
     ('Configuration', 1),
     ('also', 5),
     ('MASTER=spark://host:7077', 1),
     ('if', 4),
     ('cluster.', 1),
     ('package.)', 1),
     ('runs.', 1),
     ('page](http://spark.apache.org/documentation.html)', 1),
     ('with', 4),
     ('Data.', 1),
     ('given.', 1),
     ('[building', 1),
     ('pre-built', 1),
     ('should', 2),
     ('sample', 1),
     ('need', 1),
     ('your', 1),
     ('command,', 2),
     ('example', 3),
     ('To', 2),
     ('from', 1),
     ('same', 1),
     ('clean', 1),
     ('including', 3),
     ('can', 6),
     ('build', 3),
     ('that', 3),
     ('Maven](http://maven.apache.org/).', 1),
     ('to', 14),
     ('not', 1),
     ('Party', 1),
     ('first', 1),
     ('general', 2),
     ('environment', 1),
     ('fast', 1),
     ('build/mvn', 1),
     ('comes', 1),
     ('available', 1),
     ('(You', 1),
     ('cluster', 2),
     ('Hadoop', 4),
     ('For', 2),
     ('directory.', 1),
     ('easiest', 1),
     ('Scala', 2),
     ('data', 1),
     ('setup', 1),
     ('on', 6),
     ('prefer', 1),
     ('./bin/spark-shell', 1),
     ('running', 1),
     ('locally.', 1),
     ('which', 2),
     ('>>>', 1),
     ('requires', 1),
     ('[run', 1),
     ('-DskipTests', 1),
     ('programs,', 1),
     ('This', 2),
     ('R,', 1),
     ('built', 1),
     ('Streaming', 1),
     ('<http://spark.apache.org/>', 1),
     ('contains', 1),
     ('be', 2),
     ('More', 1),
     ('through', 1),
     ('will', 1),
     ('graphs', 1),
     ('stream', 1),
     ('system', 1),
     ('"yarn"', 1),
     ('["Building', 1),
     ('talk', 1),
     ('Big', 1),
     ('thread,', 1),
     ('./bin/pyspark', 1),
     ('web', 1),
     ('building', 3),
     ('documentation,', 1),
     ('Tests', 1),
     ('protocols', 1),
     ('submit', 1),
     ('A', 1),
     ('##', 8),
     ('<class>', 1)]



### <span style="color: red">YOUR TURN:</span> 

#### In the cell below, determine what is the most frequent word in the README, and how many times was it used?


```python
# WRITE YOUR CODE BELOW
wordCounts.reduce(lambda a, b: a if (a[1] > b[1]) else b)
```




    ('the', 21)



Highlight text field for answer:

<input type="text" size="80" value="wordCounts.reduce(lambda a, b: a if (a[1] > b[1]) else b)" style="color: white">

## Using Spark caching

In this short section, you’ll see how Spark caching can be used to pull data sets into a cluster-wide in-memory cache. This is very useful for accessing repeated data, such as querying a small “hot” dataset or when running an iterative algorithm. Both Python and Scala use the same commands.

As a simple example, let’s mark our linesWithSpark dataset to be cached and then invoke the first count operation to tell Spark to cache it. Remember that transformation operations such as cache does not get processed until some action like count() is called. Once you run the second count() operation, you should notice a small increase in speed.



```python
print(linesWithSpark.count())
```

    18



```python
from timeit import Timer
def count():
    return linesWithSpark.count()
t = Timer(lambda: count())
```


```python
print(t.timeit(number=50))
```

    8.797812205273658



```python
linesWithSpark.cache()
print(t.timeit(number=50))
```

    3.880335923982784


It may seem silly to cache such a small file, but for larger data sets across tens or hundreds of nodes, this would still work. The second linesWithSpark.count() action runs against the cache and would perform significantly better for large datasets.

<div class="alert alert-success alertsuccess" style="margin-top: 20px">
**Tip**: Enjoyed using Jupyter notebooks with Spark? Get yourself a free 
    <a href="http://cocl.us/DSX_on_Cloud">IBM Cloud</a> account where you can use Data Science Experience notebooks
    and have *two* Spark executors for free!
</div>

### Summary
Having completed this exercise, you should now be able to log in to your environment and use the Spark shell to run simple actions and transformations for Scala and/or Python. You understand that Spark caching can be used to cache large datasets and subsequent operations on it will utilize the data in the cache rather than re-fetching it from HDFS.

This notebook is part of the free course on **Cognitive Class** called *Spark Fundamentals I*. If you accessed this notebook outside the course, you can take this free self-paced course, online by going to: http://cocl.us/Spark_Fundamentals_I

### About the Authors:  
Hi! It's [Alex Aklson](https://www.linkedin.com/in/aklson/), one of the authors of this notebook. I hope you found this lab educational! There is much more to learn about Spark but you are well on your way. Feel free to connect with me if you have any questions.
<hr>
