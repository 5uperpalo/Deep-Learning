# Introduction
This repository discusses subset of machine learning tools and shows their usability in real-world use cases.

Example

## Content
* [WEKA,MOA,ELKI,Apache Pig scripts](https://github.com/5uperpalo/Big-DAMA/blob/master/Machine-Learning-Tools/weka_moa_elki_pig.md)
* [How to load data into Apache Spark](https://github.com/5uperpalo/Big-DAMA/blob/master/Machine-Learning-Tools/spark_load_data.md)
* [Apache Spark scripts](https://github.com/5uperpalo/Big-DAMA/blob/master/Machine-Learning-Tools/dev_scripts.ipynb)

Notes and will discuss following tools:
* [WEKA - Data Mining Software in Java](https://www.cs.waikato.ac.nz/ml/weka/)
* [MOA - Machine Learning for Streams](https://moa.cms.waikato.ac.nz/)
* [ELKI - Environment for Developing KDD-Applications Supported by Index-Structures](https://elki-project.github.io/)
* [Apache Pig](https://pig.apache.org/)
* [Apache Spark](https://spark.apache.org/)

in combination with :
* [Python](https://www.python.org/)
* [Jython](http://www.jython.org/)
* [Scala](https://www.scala-lang.org/)

Other popular tools:
* [Python SciPy](https://www.scipy.org/)
* [R Data Mining](http://www.rdatamining.com/)
* [Apache SAMOA - Scalable Advanced Massive Online Analysis](https://samoa.incubator.apache.org/) [the project seems to be dead]
* [Apache MAHOUT](https://mahout.apache.org/)
* Deep learning frameworks : [TensorFlow](https://www.tensorflow.org/), [TensorFlowOnSpark](https://github.com/yahoo/TensorFlowOnSpark), [H20](https://www.h2o.ai/), [Caffe](http://caffe.berkeleyvision.org/), [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark), [DL4J](https://deeplearning4j.org/), [BigDL](https://bigdl-project.github.io/)

# Introduction to java-based tools:
## [WEKA](https://www.cs.waikato.ac.nz/ml/weka/)
*"Weka is a collection of machine learning algorithms for data mining tasks. The algorithms can either be applied directly to a dataset or called from your own Java code. Weka contains tools for data pre-processing, classification, regression, clustering, association rules, and visualization. It is also well-suited for developing new machine learning schemes."*
- nice selection of common machine learning algorithms; nice/easy to use GUI, works with *.arff(also csv, json,..)* files; good documenation; well-know and tested
## [MOA](https://moa.cms.waikato.ac.nz/)
*"MOA is the most popular open source framework for data stream mining, with a very active growing community (blog). It includes a collection of machine learning algorithms (classification, regression, clustering, outlier detection, concept drift detection and recommender systems) and tools for evaluation. Related to the WEKA project, MOA is also written in Java, while scaling to more demanding problems."*
- nice selection of recent stream-based machine learning evaluation methods (Prequential, InterleavedChunks, Batch, Delayed, Cross-Valiadation etc.); large selection of stream machine learning algorithms; drift detection algorithms, evaluation metrics(accuracy, kappa, AUC, ROC); nice/easy to use GUI; works with *.arff(also csv, json,..)* files; good documenation; well-know and tested, connected to authors of WEKA
## [ELKI](https://elki-project.github.io/)
*"ELKI is an open source (AGPLv3) data mining software written in Java. The focus of ELKI is research in algorithms, with an emphasis on unsupervised methods in cluster analysis and outlier detection. ... ELKI aims at providing a large collection of highly parameterizable algorithms, in order to allow easy and fair evaluation and benchmarking of algorithms."*
- focused on clustering; great selection of well-known and lesser known clustering algorithms(including subspace, density-based, hierarchical clustering); documentation could be challenging; GUI - easy to use after you figure out some of the options(not included in documentation); most recent algorithms are available only after self compilation from github repository

# Introduction to cloud-oriented tools:
## [Apache Pig](https://pig.apache.org/)
*"Apache Pig is a platform for analyzing large data sets that consists of a high-level language for expressing data analysis programs, coupled with infrastructure for evaluating these programs. The salient property of Pig programs is that their structure is amenable to substantial parallelization, which in turns enables them to handle very large data sets."*
- commands are written in *Pig Latin* (must define schema of output for each UDF)- easy to understand but I would prefer the *"documentation for dummies"* style; possible to run in local mode for testing, but in cluster mode it works only with files in hdfs - could be challenging if you use UDF that works only with local files; possibly easy to use after initial period of struggle
- The tool is more suitable for working with text/log files files
- It supports only FOREACH cycle
- there is no way to define variables
- it can be used to call UDFs(User Defined Functions) but it is not its primary focus
- almost impossible to create a “for cycle” to work with list of files/variables
- supports Python, Jython, Java, Javascript, Ruby, Groovy UDFs
- !!! [*"With multi-query exection, you want to use STORE to save (persist) your results. You do not want to use DUMP as it will disable multi-query execution and is likely to slow down execution. (If you have included DUMP statements in your scripts for debugging purposes, you should remove them.)"*](https://pig.apache.org/docs/r0.14.0/perf.html#multi-query-execution)

- Usefull links :
	- [Things we wish we knew when we started using Pig](https://umbrella.cisco.com/blog/2013/04/08/pig-jruby/)
	- [Apache pig cheat sheet](https://www.qubole.com/resources/pig-function-cheat-sheet/)


## [Apache Spark](https://spark.apache.org/)
*"Apache Spark™ is a unified analytics engine for large-scale data processing."*
- supports Python, Java, Scala;
### [Apache Spark MLlib](https://spark.apache.org/mllib/)
*"MLlib is Apache Spark's scalable machine learning library."*
- easy to implement **distributed machine learning library**(after you understand Spark basics), confusing documentation(DataFrame-based API vs RDD-based), only the most common algorithms

# Introduction to programming languages:
## [Python](https://www.python.org/)
*"Python is a programming language that lets you work quickly and integrate systems more effectively."*
- arguably the most popular data science programming language; easy to use; ease to deploy(eg. [Anaconda](https://anaconda.org/anaconda/python), [WinPython](https://winpython.github.io/)), data science library [SciPy](https://www.scipy.org/)
## [Jython](http://www.jython.org/)
*"Python for the Java Platform"*
- easy to use, limited documentation(at least if you are not familiar with Java), ability to import *.jar(Java)* libraries and work with them in *Python-like* syntax
## [Scala](https://www.scala-lang.org/)
*"Scala combines object-oriented and functional programming in one concise, high-level language. Scala's static types help avoid bugs in complex applications, and its JVM and JavaScript runtimes let you build high-performance systems with easy access to huge ecosystems of libraries."*
- supported in many(if not all) distributed computing frameworks
- [short tutorial how to use MOA in Scala](https://moa.cms.waikato.ac.nz/using-moa-with-scala-and-its-interactive-shell/)
- kNN classifier in MOA is in the moa.classifiers.lazy.kNN package, “lazy” is a predefined keyword in scala, it must be imported by moa.classifiers.`lazy`.kNN