# Introduction

## Content
These notes will discuss following tools:
* [WEKA - Data Mining Software in Java](https://www.cs.waikato.ac.nz/ml/weka/)
* [MOA - Machine Learning for Streams](https://moa.cms.waikato.ac.nz/)
* [ELKI - Environment for Developing KDD-Applications Supported by Index-Structures](https://elki-project.github.io/)
* [Apache Pig](https://pig.apache.org/)
* [Apache Spark](https://spark.apache.org/)

in combination with :
* [Python](https://www.python.org/)
* [Jython](http://www.jython.org/)

Other popular tools:
* [Python SciPy](https://www.scipy.org/)
* [R Data Mining](http://www.rdatamining.com/)
* [Apache SAMOA - Scalable Advanced Massive Online Analysis](https://samoa.incubator.apache.org/) [the project seems to be dead]
* [Apache MAHOUT](https://mahout.apache.org/)
* Deep learning frameworks : [TensorFlow](https://www.tensorflow.org/), [TensorFlowOnSpark](https://github.com/yahoo/TensorFlowOnSpark), [H20](https://www.h2o.ai/), [Caffe](http://caffe.berkeleyvision.org/), [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark), [DL4J](https://deeplearning4j.org/), [BigDL](https://bigdl-project.github.io/)


Apache Pig script:

	REGISTER '/home/user/moa-release-2018.6.0/lib/moa.jar';
	REGISTER 'test_jython.py' USING jython AS moaudf;
	SET mapred.cache.files hdfs:///user/user/dataset.arff#filename;
	SET mapred.createsymlink YES;
	#textfile contains just the name of the dataset
	a = LOAD '/user/user/textfile.txt' AS (x:chararray);
	RESULT = FOREACH a GENERATE moa.evaluation_knn(x,'filename');
	dump RESULT;

