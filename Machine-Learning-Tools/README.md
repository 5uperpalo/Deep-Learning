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

Compress/pack and copy files to hdfs:

	# tar gunzip the files
	# what switches mean: 
	#    -c: Create an archive.
	#    -z: Compress the archive with gzip.
	#    -v: Display progress in the terminal while creating the archive, also known as “verbose” mode. The v is always optional in these commands, but it’s helpful.
	#    -f: Allows you to specify the filename of the archive.
	sudo tar -czvf mawi_data_long.tar.gz ./mawi*
	# copy the file to hdfs location
	hadoop fs -copyFromLocal mawi_data_long.tar.gz /user/big-dama/pavol/mawi_data_long.tar.gz
	
Unpack the compressed file to distributed cache and work with it as with folder through simlink:

	SET mapred.createsymlink YES;
	SET mapred.cache.archives hdfs:///user/big-dama/pavol/mawi_data_long.tar.gz#mawi_dataset;
	REGISTER 'my.py' USING jython AS myudf;
	a = LOAD '/user/big-dama/pavol/pig_file.pig' as (x:chararray);
	result = FOREACH a GENERATE myudf.list_files(x,'mawi_dataset');
	DUMP result;

Jython MYUDF:

	#/usr/bin/env python
	import os

	@outputSchema("ls:chararray")
	def list_files(x,f):
        ls =  os.listdir('.')
        fin = open(f,'rb')
        #return [x,fin.readlines()]
        return [x,str(ls)]
	
Jython MOA UDF:
	#/usr/bin/env python

	import moa.streams.ArffFileStream as arff_stream
	import moa.evaluation.AdwinClassificationPerformanceEvaluator as adwin
	import moa.classifiers.lazy.kNN as knn
	import java.io.IOException
	import moa.tasks.EvaluatePrequentialCV as evaluate

	@outputSchema("word:chararray")
	def evaluation_knn(x,word):
		stream = arff_stream(word,-1)
        stream.prepareForUse()
        eval = evaluate()
        eval.streamOption.setCurrentObject(stream)
        learner=knn()
        eval.learnerOption.setCurrentObject(learner)
        evalopt=adwin()
        eval.evaluatorOption.setCurrentObject(evalopt)
        eval.prepareForUse()
        a=eval.doTask()
        b=str(a)
        return [x,b]
		
MOA oneliner:

Same evaluation/classification as a oneliner that you can put to bash script and run with different parameters and analyse the results later in python. You can see the whole command in MOA GUI you just have to be in moa/lib folder (for a current version 2018.6.0) and prepend "java -cp moa.jar -javaagent:sizeofag-1.0.4.jar moa.DoTask". File sizeofag-1.0.4.jar is being used to calculate RAM memory time(s)
NOTE:
in case of a windows 10 machine you may use "new" bash shell, you just need to navigate to correct directory + change \ to /

	java -cp moa.jar -javaagent:sizeofag-1.0.4.jar moa.DoTask EvaluatePrequentialCV -l lazy.kNN -s (clustering.FileStream -f C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff) -e AdwinClassificationPerformanceEvaluator
	
Python script to aggregate the results to one table/file for further analysis:

	import os
	import numpy as np

	aggregated_file = np.array()
	path_2_files = "C:/#CVUT/AIT_internship/MOA/results/InterleavedChunks/27/a/" "PATH"
	for file in os.listdir(path_2_files)
		if aggregated_file:aggregated_file.append([p.genfromtxt(path_2_files + file, delimiter=',')])