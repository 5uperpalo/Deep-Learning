# How to load data into Apache Spark

* Apache Spark tries to load data from hdfs by default, but this can be changed by explicitely stating a path to file. I didn't test it, but I guess it works when spark is in local mode, but you have to copy it on all nodes in case of yarn client/cluster mode.
See following [response](https://stackoverflow.com/questions/27299923/how-to-load-local-file-in-sc-textfile-instead-of-hdfs) for detailed explanation on how to load local file:
"Try explicitly specify sc.textFile("file:///path to the file/"). The error occurs when Hadoop environment is set. SparkContext.textFile internally calls org.apache.hadoop.mapred.FileInputFormat.getSplits, which in turn uses org.apache.hadoop.fs.getDefaultUri if schema is absent. This method reads "fs.defaultFS" parameter of Hadoop conf. If you set HADOOP_CONF_DIR environment variable, the parameter is usually set as "hdfs://..."; otherwise "file://"."
* [Databricks](https://docs.databricks.com/spark/latest/data-sources/index.html) provides nice guide/summary on different data sources in Apache Spark
* Streaming : [Cloudera](https://www.cloudera.com/documentation/spark2/latest/topics/spark2_known_issues.html) should support only DStreams(RDD stream). This information is related to Apache Spark 2.0, we are using v2.3 and it seems it is supporting [structured streaming("stream processing engine built on the Spark SQL engine")](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) - I tested it.


