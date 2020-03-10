# **USE CASE** : Run java-based tool with multiple tasks and gather the results to single table for furher analysis in Python[local mode]:
Usefull for initial troubleshooting, non-intensive computations, figuring out the range of algorithm parameters that make sense for particular issue.
## Howto (bash script):
1. pick tasks you want to do in a prefered tool (WEKA, MOA, ELKI)
2. choose the correct options to write the results into output files (good choice is to keep to some directory/file naming structure for easier aggregating of the data)
3. copy the the commands to textfile
4. run the textfile as .bat(Windows) or as a bash script with one task per line
5. aggregate the results into one table/file by python script

Notes:
- Windows 10 supports built in bash environment(or you may use the Cygwin or other shell emulation)
	- if you are using GUI in Win to genrate oneliners and then want to run them in bash, don't forget to adjust the path and replace "\" with "/"
- Beware the EOL symbol! - textfile prepared in Windows has different EOL than UNIX(better stick to copy/paste than copy the file from Win to UX and vice versa)
- "nohup ./script &" is your friend (runs the script in the background and you can go for a beer in the meantime ;) )


### Commands/scripts
You have to either run the .jar file or include it in the classpath:

	C:\Users\mulinkap>java
	Usage: java [-options] class [args...]
				(to execute a class)
		or  java [-options] -jar jarfile [args...]
				(to execute a jar file)
	where options include:
		-d32		use a 32-bit data model if available
		-d64		use a 64-bit data model if available
		-server		to select the "server" VM
					The default VM is server.

		**-cp <class search path of directories and zip/jar files>**
	...
#### [WEKA](https://www.cs.waikato.ac.nz/ml/weka/)
*"Since version 3.5.3, one can right-click (or <Alt>+<Shift> left-click for Mac users) any GenericObjectEditor panel and select the Copy configuration to clipboard option to copy the currently shown configuration to the clipboard and then just paste it into the commandline. One only needs to add the appropriate java call and other general options, like datasets, class index, etc."* For more information read the *[How to run WEKA schemes from CLI](https://weka.wikispaces.com/How+to+run+WEKA+schemes+from+commandline)*.
But to get the whole command you have to prepend *"java -cp weka.jar"* and append *"-t "PATH_TO_FILE/FILE.arff""*. See *[brief introduction to using WEKA from the CLI*](https://weka.wikispaces.com/Primer)*.

oneliner example:

	# Default evaluation(10 folds Cross-validation) of K-nearest neighbours classifier. Can select appropriate value of K based on cross-validation. Can also do distance weighting. See [documentation](http://weka.sourceforge.net/doc.dev/weka/classifiers/lazy/IBk.html).
	# command : java -cp weka.jar weka.classifiers.lazy.IBk -K 1 -W 0 -A "weka.core.neighboursearch.LinearNNSearch -A \"weka.core.EuclideanDistance -R first-last\"" -t "C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff"
	# example :
	C:\Program Files\Weka-3-9>java -cp weka.jar weka.classifiers.lazy.IBk -K 1 -W 0 -A "weka.core.neighboursearch.LinearNNSearch -A \"weka.core.EuclideanDistance -R first-last\"" -t "C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff"

	Options: -K 1 -W 0 -A "weka.core.neighboursearch.LinearNNSearch -A "weka.core.EuclideanDistance -R first-last""

	=== Classifier model (full training set) ===

	IB1 instance-based classifier using 1 nearest neighbour(s) for classification
	...

#### [MOA](https://moa.cms.waikato.ac.nz/)
You can see the whole command in MOA GUI you just have to be in moa/lib folder and prepend "java -cp moa.jar -javaagent:sizeofag-1.0.4.jar moa.DoTask" (tested in current version 2018.6.0). File sizeofag-1.0.4.jar is being used to calculate RAM memory time(s) and in most of the cases is not important but generates a WARNING if not included.

oneliner example:

	# Prequential CV evaluation of kNN on mawi_data_1_.long.arff dataset with ADWIN classification, output - stdout
	# command : java -cp moa.jar -javaagent:sizeofag-1.0.4.jar moa.DoTask EvaluatePrequentialCV -l lazy.kNN -s (clustering.FileStream -f C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff) -e AdwinClassificationPerformanceEvaluator
	# example :
	C:\#CVUT\AIT_internship\MOA\moa-release-2018.6.0\lib>java -cp moa.jar -javaagent:sizeofag-1.0.4.jar moa.DoTask EvaluatePrequentialCV -l lazy.kNN -s (clustering.FileStream -f C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff) -e AdwinClassificationPerformanceEvaluator

	{M}assive {O}nline {A}nalysis
	Version:  18.06 June 2018
	Copyright: (C) 2007-2018 University of Waikato, Hamilton, New Zealand
	Web: http://moa.cms.waikato.ac.nz/
	
	
	Task completed in 2m54s (CPU time)
	
	learning evaluation instances,evaluation time (cpu seconds),model cost (RAM-Hours),[avg] classified instances,[err] classified instances,[avg] classifications correct (percent),[err] classifications correct (percent),[avg] Kappa Statistic (percent),[err] Kappa Statistic (percent),[avg] Kappa Temporal Statistic (percent),[err] Kappa Temporal Statistic (percent),[avg] Kappa M Statistic (percent),[err] Kappa M Statistic (percent)
	2700.0,170.9146956,0.007101881346157565,2700.0,0.0,63.49999999999999,1.1992796191623805,9.532113423354991,5.3485833046085505,-16324.999999999896,539.6758286230685,-562.615384615385,21.771537701717094

#### [ELKI](https://elki-project.github.io/)
ELKI is primarly focused on clustering. GUI seems easy to understand at first glance, but at first you really have to search for options - eg. different evalautions, how/where to ouput the results etc. The command is displayed in GUI and you have to only prepend *"java -jar elki.jar"*.

oneliner example:
	
	# as ELKI focused on clustering and KNN is classification algorithm I included an example wit K-Means algorithm(k=10) that outputs the clustering results to stdout
	# command : java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 10 -resulthandler ClusteringVectorDumper
	# example : 
	C:\#CVUT\AIT_internship\ELKI>java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 10 -resulthandler ClusteringVectorDumper
	de.lmu.ifi.dbs.elki.datasource.FileBasedDatabaseConnection.parse: 220 ms
	de.lmu.ifi.dbs.elki.datasource.FileBasedDatabaseConnection.filter: 0 ms
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.initialization: de.l
	mu.ifi.dbs.elki.algorithm.clustering.kmeans.initialization.RandomlyChosenInitialMeans@3834d63f
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 4.3129983233690116E24
	...
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.runtime: 760 ms
	1 7 5 1 1 1 1 1 1 1 1 1 7 1 1 1 5 1 5 1 5 5 1 1 1 1 1 1 1 1 7 1 1 1 1 1 1 1 1 5
	...
	
	2 9 9 4 4 4 2 9 9 9 4 1 9 3 4 2 2 2 4 9 k-Means Clustering

#### Python script to aggregate the results into one table/file for further analysis:
This is an example script to aggregate results from multiple ELKI computations. Each computation creates an "internal evaluation.txt" results file in a new directory. This script puts results into columns, where each column represents 1 directory(parameter combination for a script).
The script can be easiy adjusted for different tasks depending on a tool and results we are analyzing like aggregate clustering results or evaluation results ffrom WEKA/MOA.

	C:\#CVUT\AIT_internship\ELKI>type aggregate_script.py
	import os
	import pandas as pd
	import sys

	# usage:
	# aggregate_script.py "PATH_RESULT_DRECTORIES" "RESULTS_FILE_NAME"
	#
	# example variables
	# path_2_results = "C:/#CVUT/AIT_internship/ELKI/results/"
	# results_file = "internal evaluation.txt"
	#
	# example command:
	# python aggregate_script.py "C:/#CVUT/AIT_internship/ELKI/results/" "internal evaluation.txt"

	path_2_results = sys.argv[1]
	results_file = sys.argv[2]
	list_of_dirs = os.listdir(path_2_results)
	joining_delimiter = " "
	final_df = pd.DataFrame()

	if __name__ == '__main__':
		for directory in list_of_dirs:
			temp_list=[]
			row_names=[]
			for line in open(path_2_results + directory + "/" + results_file):
				temp_line=line.split(' ')
				#exclude non-result lines that start with #
				if temp_line[0] != "#":
					# [-1] - last element, [:-1] - whole string/number except traling EOL"\n"
					temp_list.append(temp_line[-1][:-1])
					row_names.append(joining_delimiter.join(temp_line[:-1]))
				# generate pandas data frame
			if final_df.empty:
				final_df=pd.Series(temp_list)
			else:
				final_df = pd.concat([final_df, pd.Series(temp_list)], axis=1)
		final_df.columns = list_of_dirs
		final_df.index = row_names
		print(final_df)

### Example	for aggregation of 2 results files from ELKI:

	C:\#CVUT\AIT_internship\ELKI>type elki_script.bat
	java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 10 -evaluator clustering.internal.EvaluateSquaredErrors -resulthandler ResultWriter -out "C:\\#CVUT\\AIT_internship\\ELKI\\results\\10means"
	java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 20 -evaluator clustering.internal.EvaluateSquaredErrors -resulthandler ResultWriter -out "C:\\#CVUT\\AIT_internship\\ELKI\\results\\20means"
	C:\#CVUT\AIT_internship\ELKI>elki_script.bat
	
	C:\#CVUT\AIT_internship\ELKI>java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 10 -evaluator clustering.internal.EvaluateSquaredErrors -resulthandler ResultWriter -out "C:\\#CVUT\\AIT_internship\\ELKI\\results\\10means"
	de.lmu.ifi.dbs.elki.datasource.FileBasedDatabaseConnection.parse: 254 ms
	de.lmu.ifi.dbs.elki.datasource.FileBasedDatabaseConnection.filter: 0 ms
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.initialization: de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.initialization.RandomlyChosenInitialMeans@6ea6d14e
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 4.964909899684822E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 4.4276087419596955E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 4.1245219342708334E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 3.840605318167316E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 3.5086407304464806E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 3.206894436527583E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 2.8697855526400535E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 2.568806458257992E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 2.356157564187442E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 2.1240557959573565E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.9040572581253092E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.7540111908544188E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.6766021712925256E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.6145458402558166E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.5822286829427827E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.5494641112483177E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.4903377694407812E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.4523072113961283E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.431860594626466E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.415922529866613E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.4007852352452707E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.3833410026793218E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.3591157137492283E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.3372261949617808E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.3221094483426967E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.3008155567405128E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.2812329282241645E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.2564041619343294E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.2348046257621702E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.2198849505598908E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.1787780284710123E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.1048981082727971E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.046843900993726E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 9.12166877654071E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.83612282349309E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.694201809889413E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.606117649355216E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.478221851883568E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.383144556540048E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.242411263209579E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.094285615889891E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.976348520823776E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.842839110698258E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.750287100698588E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.675708318424761E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.632215189128974E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.591982761063193E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.518472583978285E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.394068987408488E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.244918775020782E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.057039702484971E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.9390836751236E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.849348737974314E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.765118300315328E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.692370993307364E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.651424075567595E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.633559172661744E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.611432398728511E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.587203202571605E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.566033893580664E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.553669230167157E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.542778095436965E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.532210595696634E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.514663379496467E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.5025150965172E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.486624748433443E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.464694283308194E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.455158413156087E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.449386194682817E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.448188403975766E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.447791188593715E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.446061181448838E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.444459129754955E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.441542819039688E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.43957341076408E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.437832142650324E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.436667432490457E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.435665038641532E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.435305253513972E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.435133612977143E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.434931217361692E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.434506813978579E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.434038940266024E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.43375231320773E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.433322381611083E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.433225376425907E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.iterations: 85
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.runtime: 1062 ms
	de.lmu.ifi.dbs.elki.evaluation.clustering.internal.EvaluateSquaredErrors.mean: 2.382676065342929E20
	de.lmu.ifi.dbs.elki.evaluation.clustering.internal.EvaluateSquaredErrors.ssq: 6.433225376425908E23
	de.lmu.ifi.dbs.elki.evaluation.clustering.internal.EvaluateSquaredErrors.rmsd: 1.5435919361485823E10
	
	C:\#CVUT\AIT_internship\ELKI>java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 20 -evaluator clustering.internal.EvaluateSquaredErrors -resulthandler ResultWriter -out "C:\\#CVUT\\AIT_internship\\ELKI\\results\\20means"
	de.lmu.ifi.dbs.elki.datasource.FileBasedDatabaseConnection.parse: 250 ms
	de.lmu.ifi.dbs.elki.datasource.FileBasedDatabaseConnection.filter: 0 ms
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.initialization: de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.initialization.RandomlyChosenInitialMeans@6ea6d14e
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 2.258032553843358E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.8600365104538105E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.6333485211338183E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.4148085781367522E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.3092255797402257E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.266301774799354E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.223050840140631E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.1911314335119479E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.162616982854515E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.143674082801014E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.1287955281874423E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.0833686474335987E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 1.016058545026427E24
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 9.609500224508205E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 8.261517577567002E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.923348008848282E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.696626399095408E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.291992974787124E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 7.036912511746952E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.879518367039177E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.71543867604661E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.538458196017976E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.353558529195436E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.186880660745424E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.093981553597858E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 6.0226316823559355E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.9821568400655E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.9452258989965617E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.905257394200985E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.870267344504967E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.8143994649348807E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.7670391248998754E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.737748517729547E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.715098908163546E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.6974000677033514E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.6783442118395106E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.6578787514635125E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.611705082703447E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.541564391926976E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.49468952149251E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.4134243198834355E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.3406222701437016E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.3069948874084254E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.288626025560183E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.279700393128085E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.273275935371178E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.2645823813975605E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.2548136280627855E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.248145585555817E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.240629363781206E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.231102641722467E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.221416954743901E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.2115660859236075E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.204236025104636E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.197291861704428E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1899836459392576E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.182698394134354E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.176041438452478E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.169354397261144E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.165430927234376E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1581844702583664E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1533644838696457E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.148178273216889E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1424033712232066E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1387457896422314E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.137035965637919E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1355488800780957E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.133689314291815E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.130640756489396E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.127109640305908E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.123758440181249E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1217248201131984E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.12016000296191E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1180471686574765E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1160059330578134E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.114472458441478E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.113446508624984E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.112881353915885E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.112421053718952E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.112084254524051E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.111711483917833E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1113997429810945E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.110992771403314E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.110489615931427E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1098192877501094E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.108939606661864E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.108395435607095E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1079491246264465E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.107397696679052E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.1066561810012836E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.105296747073424E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.103760244823199E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.102458161244418E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.101511464968801E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.10061234586269E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0996144156449016E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.098739954658641E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.098001087094229E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0972509724061914E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.096694369564425E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.09600993883442E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.09517585941874E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.094073278339852E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0933330915717223E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.092533190793921E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0912010013066035E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.089371080200837E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.088454675215953E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0870604850418936E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0847892362171514E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.08124886211317E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.078962909724801E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.077111001128523E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.074033546068084E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0714574276433805E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0705628573254E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.069871415906564E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.068978138135884E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.068004547602384E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.066784500067221E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.065492735785666E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.064846909337949E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.064370281658878E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.064176235949023E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.063824323081122E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.063244027240488E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.062886422266114E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.062761307890428E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.062649287548251E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0625655314673606E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.06238656816446E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.062255639343548E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0621394085952846E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0619417449670396E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.061746677020488E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.061489933058826E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.061224805310154E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.06081711972658E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.060330227026476E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.059780908101518E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.059378517902124E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.059179314775565E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.058885004980119E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0586615403204204E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.058297119989887E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.057717054712448E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0572076840288386E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.056933265086872E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.056595643770976E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.05611107464978E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.055534726005517E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.054833603638218E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.05400651939886E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.05293516737821E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0520731524657294E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.051451194135935E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.050888734144255E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0503795609339305E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0498560954668775E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.049435383841706E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0489885428887276E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.048547742087297E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0481501747373685E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.047769908471476E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.047583783522439E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0475000433198626E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0473823062750654E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0471003273751495E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046858783658808E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046652223960446E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0465709437950996E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0464824540678014E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046359125806657E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046304616987042E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046256580342695E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046238914498209E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0462117090984855E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046201275025233E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046191606336633E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046167991078897E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046124729497937E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0460866075305E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046072809582765E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0460594968130016E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046042747581693E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046025579138239E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.046020519488726E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.variance-sum: 5.0460178466108605E23
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.iterations: 187
	de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd.runtime: 3320 ms
	de.lmu.ifi.dbs.elki.evaluation.clustering.internal.EvaluateSquaredErrors.mean: 1.8688954987447645E20
	de.lmu.ifi.dbs.elki.evaluation.clustering.internal.EvaluateSquaredErrors.ssq: 5.046017846610864E23
	de.lmu.ifi.dbs.elki.evaluation.clustering.internal.EvaluateSquaredErrors.rmsd: 1.3670755278128435E10
	
	C:\#CVUT\AIT_internship\ELKI>python aggregate_script.py "C:/#CVUT/AIT_internship/ELKI/results/" "internal evaluation.txt
	                              10means                20means
	Mean distance    2.382676065342929E20  1.8688954987447645E20
	Sum of Squares   6.433225376425908E23   5.046017846610864E23
	RMSD            1.5435919361485823E10  1.3670755278128435E10
	
	C:\#CVUT\AIT_internship\ELKI>
		
## Howto (Jython):
The procedure is same as in previous section "Howto (scripts)", but we will work in Jython interactive shell.

procedure:
1. pick tasks you want to do in a prefered tool (WEKA, MOA, ELKI)
2. choose the options
3. find the options in the documentation and import them as functions
4. export the tool you are using into $CLASSPATH
5. run the code(as standalone, as a function, or in interactive shell by typing "jython")
5. aggregate the results into one table/file

### Jython interactive shell:
#### [WEKA](https://www.cs.waikato.ac.nz/ml/weka/)
First you need to export weka.jar into CLASSPATH:
	export CLASSPATH=$CLASSPATH:/home/big-dama/weka.jar
	
We wil transform weka oneliner that we used previously into jython code through jython interactive shell.

oneliner(again):
	
	java -cp weka.jar weka.classifiers.lazy.IBk -K 1 -W 0 -A "weka.core.neighboursearch.LinearNNSearch -A \"weka.core.EuclideanDistance -R first-last\"" -t "C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff"

Jython code(inspired by [this](https://weka.wikispaces.com/Using+WEKA+from+Jython) and [this](http://weka.wikispaces.com/Generating+classifier+evaluation+output+manually):
The easiest way is to copy the options from oneliner and set them to classifier, but you may also call separate fuctions as listed by dir() in code. The documentation can be found [here](http://weka.sourceforge.net/doc.dev/weka/classifiers/lazy/IBk.html)
!WARNING! : you may have to retype "-" symbols as it seems there are two symbols with different ascii code - even though they look same.
	
	big-dama@bigdama-vworker1-phy7:~/pavol$ export CLASSPATH=$CLASSPATH:/home/big-dama/pavol/weka.jar
	big-dama@bigdama-vworker1-phy7:~/pavol$ jython
	"my" variable $jythonHome masks earlier declaration in same scope at /usr/bin/jython line 15.
	*sys-package-mgr*: processing new jar, '/home/big-dama/pavol/weka.jar'
	Jython 2.5.3 (, Sep 21 2017, 03:12:48)
	[Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)] on java1.8.0_171
	Type "help", "copyright", "credits" or "license" for more information.
	>>>
	>>> import weka.classifiers.lazy.IBk as knn
	>>> knn_classifier = knn()
	>>> dir(knn_classifier)
	['BATCH_SIZE_DEFAULT', 'KNN', 'KNNTipText', 'NUM_DECIMAL_PLACES_DEFAULT', 'TAGS_WEIGHTING', 'WEIGHT_INVERSE', 'WEIGHT_NONE', 'WEIGHT_SIMILARITY', '__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'batchSize', 'batchSizeTipText', 'buildClassifier', 'capabilities', 'class', 'classifyInstance', 'crossValidate', 'crossValidateTipText', 'debug', 'debugTipText', 'distanceWeighting', 'distanceWeightingTipText', 'distributionForInstance', 'distributionsForInstances', 'doNotCheckCapabilities', 'doNotCheckCapabilitiesTipText', 'enumerateMeasures', 'equals', 'forName', 'getBatchSize', 'getCapabilities', 'getClass', 'getCrossValidate', 'getDebug', 'getDistanceWeighting', 'getDoNotCheckCapabilities', 'getKNN', 'getMeanSquared', 'getMeasure', 'getNearestNeighbourSearchAlgorithm', 'getNumDecimalPlaces', 'getNumTraining', 'getOptions', 'getRevision', 'getTechnicalInformation', 'getWindowSize', 'globalInfo', 'hashCode', 'implementsMoreEfficientBatchPrediction', 'listOptions', 'main', 'makeCopies', 'makeCopy', 'meanSquared', 'meanSquaredTipText', 'nearestNeighbourSearchAlgorithm', 'nearestNeighbourSearchAlgorithmTipText', 'notify', 'notifyAll', 'numDecimalPlaces', 'numDecimalPlacesTipText', 'numTraining', 'options', 'postExecution', 'preExecution', 'pruneToK', 'revision', 'run', 'runClassifier', 'setBatchSize', 'setCrossValidate', 'setDebug', 'setDistanceWeighting', 'setDoNotCheckCapabilities', 'setKNN', 'setMeanSquared', 'setNearestNeighbourSearchAlgorithm', 'setNumDecimalPlaces', 'setOptions', 'setWindowSize', 'technicalInformation', 'toString', 'updateClassifier', 'wait', 'windowSize', 'windowSizeTipText']
	>>> knn_classifier.setOptions(["-K","1","-W","10","-A","LinearNNSearch -A \"EuclideanDistance -R first-last\""])
	>>>	
	>>> import java.io.FileReader as FileReader	
	>>> file = FileReader("/home/big-dama/pavol/mawi_datasets/mawi_data_1_long.arff")
	>>>
	>>> import weka.core.Instances as Instances
	>>> data = Instances(file)
	>>> data.setClassIndex(data.numAttributes() - 1) # sets the last column as "class column"
	>>>	
	>>> import weka.classifiers.Evaluation as evaluation
	>>> eval = evaluation(data)
	>>> 	
	>>> from java.util import Random	
	>>> rand = Random(1) # using seed = 1
	>>> folds = 10
	>>> eval.crossValidateModel(knn_classifier,data,folds,rand)
	>>> print(eval.toSummaryString())

	Correctly Classified Instances        1958               72.5185 %
	Incorrectly Classified Instances       742               27.4815 %
	Kappa statistic                          0.3832
	Mean absolute error                      0.3123
	Root mean squared error                  0.4858
	Relative absolute error                 68.248  %
	Root relative squared error            101.5488 %
	Total Number of Instances             2700
	
	# if you want to build a model and classify new values, you must build the classifier
	>>> knn_classifier.buildClassifier(data)
	>>> print(knn_classifier)
	IB1 instance-based classifier
	using 1 nearest neighbour(s) for classification
	using a maximum of 10 (windowed) training instances
	
#### [MOA](https://moa.cms.waikato.ac.nz/)

MOA functions are [documented](https://www.cs.waikato.ac.nz/~abifet/MOA/API/classmoa_1_1options_1_1_list_option.html) in less detail than WEKA, so it is easier to set the individual options than list of options as with WEKA.

oneliner(again):

	java -cp moa.jar -javaagent:sizeofag-1.0.4.jar moa.DoTask EvaluatePrequentialCV -l lazy.kNN -s (clustering.FileStream -f C:\#CVUT\AIT_internship\MOA\datasets\mawi_samples\mawi_data_1_long.arff) -e AdwinClassificationPerformanceEvaluator
	
Jython code(inspired by WEKA code in previous subsection):

	big-dama@bigdama-vworker1-phy7:~/pavol$ export JAVA_TOOL_OPTIONS=-javaagent:/home/big-dama/pavol/moa-release-2018.6.0/lib/sizeofag-1.0.4.jar
	big-dama@bigdama-vworker1-phy7:~/pavol$ export CLASSPATH=$CLASSPATH:/home/big-dama/pavol/moa-release-2018.6.0/lib/moa.jar
	big-dama@bigdama-vworker1-phy7:~/pavol$ jython
	"my" variable $jythonHome masks earlier declaration in same scope at /usr/bin/jython line 15.
	*sys-package-mgr*: processing new jar, '/home/big-dama/pavol/moa-release-2018.6.0/lib/moa.jar'
	Jython 2.5.3 (, Sep 21 2017, 03:12:48)
	[Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)] on java1.8.0_171
	Type "help", "copyright", "credits" or "license" for more information.
	>>>
	>>> import moa.classifiers.lazy.kNN as knn
	>>> knn_classifier = knn()
	>>> dir(knn_classifier)
	['AWTRenderer', '__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'class', 'classNameString', 'classifierRandom', 'contextIsCompatible', 'copy', 'correctlyClassifies', 'equals', 'getAWTRenderer', 'getAttributeNameString', 'getCLICreationString', 'getClass', 'getClassLabelString', 'getClassNameString', 'getDescription', 'getModel', 'getModelContext', 'getModelDescription', 'getModelMeasurements', 'getNominalValueString', 'getOptions', 'getPredictionForInstance', 'getPurposeString', 'getSubClassifiers', 'getSublearners', 'getVotesForInstance', 'hashCode', 'isRandomizable', 'kOption', 'limitOption', 'measureByteSize', 'model', 'modelContext', 'modelMeasurements', 'nearestNeighbourSearchOption', 'notify', 'notifyAll', 'options', 'prepareForUse', 'prepareForUseImpl', 'purposeString', 'randomSeed', 'randomizable', 'resetLearning', 'resetLearningImpl', 'setModelContext', 'setRandomSeed', 'subClassifiers', 'sublearners', 'toString', 'trainOnInstance', 'trainOnInstanceImpl', 'trainingHasStarted', 'trainingWeightSeenByModel', 'wait']
	>>>
	>>> import moa.streams.ArffFileStream as arff_stream
	>>> file = "/home/big-dama/pavol/mawi_datasets/mawi_data_1_long.arff"
	>>> stream = arff_stream(file,-1) # specify last column as class column(-1)
	>>> stream.prepareForUse()
	>>>
	>>> import moa.tasks.EvaluatePrequentialCV as evaluate
	>>> eval = evaluate()
	>>> dir(eval)
	['__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'class', 'copy', 'doTask', 'dumpFileOption', 'equals', 'evaluatorOption', 'eventsList', 'getCLICreationString', 'getClass', 'getDescription', 'getEvaluationMeasurements', 'getEventsList', 'getOptions', 'getPurposeString', 'getTaskName', 'getTaskResultType', 'hashCode', 'instanceLimitOption', 'learnerOption', 'measureByteSize', 'memCheckFrequencyOption', 'notify', 'notifyAll', 'numFoldsOption', 'options', 'outputFileOption', 'prepareForUse', 'purposeString', 'randomSeedOption', 'sampleFrequencyOption', 'streamOption', 'taskName', 'taskResultType', 'timeLimitOption', 'toString', 'validationMethodologyOption', 'wait']
	>>> eval.streamOption.setCurrentObject(stream)
	>>> eval.learnerOption.setCurrentObject(knn_classifier)
	>>>
	>>> import moa.evaluation.AdwinClassificationPerformanceEvaluator as adwin
	>>> evalopt = adwin()
	>>> eval.evaluatorOption.setCurrentObject(evalopt)
	>>> eval.prepareForUse()
	>>> eval.doTask()

	Can not access instrumentation environment.
	Please check if jar file containing SizeOfAgent class is
	specified in the java's "-javaagent" command line argument.
	learning evaluation instances,evaluation time (cpu seconds),model cost (RAM-Hours),[avg] classified instances,[err] classified instances,[avg] classifications correct (percent),[err] classifications correct (percent),[avg] Kappa Statistic (percent),[err] Kappa Statistic (percent),[avg] Kappa Temporal Statistic (percent),[err] Kappa Temporal Statistic (percent),[avg] Kappa M Statistic (percent),[err] Kappa M Statistic (percent)
	2700.0,430.211577652,0.0,2700.0,0.0,63.49999999999999,1.1992796191623805,9.532113423354991,5.3485833046085505,-16324.999999999896,539.6758286230685,-562.615384615385,21.771537701717094	

Scala code of evaluation presented in *SIGCOMM'18 Workshop on Big Data Analytics and Machine Learning for Data Communication Networks (Big-DAMA 2018) "Stream-based Machine Learning for Network Security and Anomaly Detection"* paper:
	//:require /home/big-dama/pavol/moa-release-2018.6.0/lib/moa.jar

	import moa.tasks.EvaluatePrequentialCV
	import java.io.IOException

	// definition of extended classes aka definition of default parameters we are going to use in evaluation
	class tenCV_adwin_evaluation(input_file_location: String) extends EvaluatePrequentialCV(){
        val stream = "ArffFileStream -f (" + input_file_location + ")";
        val evaluation_opt = "AdwinClassificationPerformanceEvaluator";
        streamOption.setValueViaCLIString(stream);
		evaluatorOption.setValueViaCLIString(evaluation_opt);
	}

	class auc_evaluation(input_file_location: String, auc_window: String) extends EvaluatePrequentialCV(){
        val stream = "ArffFileStream -f (" + input_file_location + ")";
        val evaluation_opt = "WindowAUCImbalancedPerformanceEvaluator -w " + auc_window;
        streamOption.setValueViaCLIString(stream);
        evaluatorOption.setValueViaCLIString(evaluation_opt);
	}

	// lists of "datasets,methods and evaluation paramters" over which we are going to iterate
	val mawi_datasets_list = List("/home/big-dama/pavol/mawi_datasets/mawi_data_1_long.arff", "/home/big-dama/pavol/mawi_datasets/mawi_data_2_long.arff", "/home/big-dama/pavol/mawi_datasets/mawi_data_3_long.arff", "/home/big-dama/pavol/mawi_datasets/mawi_data_4_long.arff", "/home/big-dama/pavol/mawi_datasets/mawi_data_5_long.arff");
	val learner_list = List[String]("lazy.kNN -k 10", "meta.AdaptiveRandomForest", "trees.HoeffdingAdaptiveTree", "functions.SGD");
	val auc_list = List[String]("270", "540", "810", "1080", "1350", "1620", "1890", "2160", "2430", "2700");
	// header from results with prepended dataset, algorithm column
	var result_table = List(List("learner, dataset, auc window, learning evaluation instances, evaluation time (cpu seconds), model cost (RAM-Hours), [avg] classified instances, [err] classified instances, [avg] classifications correct (percent), [err] classifications correct (percent), [avg] Kappa Statistic (percent), [err] Kappa Statistic (percent), [avg] Kappa Temporal Statistic (percent), [err] Kappa Temporal Statistic (percent), [avg] Kappa M Statistic (percent), [err] Kappa M Statistic (percent)"));

	// computation start time
	val startTime = System.nanoTime();
	for (mawi_dataset <- mawi_datasets_list){
        var result = new tenCV_adwin_evaluation(mawi_dataset);
        for (learner <- learner_list){
			result.learnerOption.setValueViaCLIString(learner);
            result.prepareForUse();
            // converts result(type Object) to type String
            var result_string : String = result.doTask()+"";
            // splits the resulting string by , or \n(new line symbol after the header in result)
            var result_list = result_string.split("[\n,]").drop(13).toList;
            // prepend learner, dataset name, auc window width values to results
            result_table = result_table :+ (learner +: (mawi_dataset.split("/").last +:("NA" +: result_list)));
        }
		for (auc_window_width <- auc_list){
			var result = new auc_evaluation(mawi_dataset,auc_window_width);
			for (learner <- learner_list){
				result.learnerOption.setValueViaCLIString(learner);
				result.prepareForUse();
				// converts result(type Object) to type String
				var result_string : String = result.doTask()+"";
				// splits the resulting string by , or \n(new line symbol after the header in result)
				var result_list = result_string.split("[\n,]").drop(13).toList;
				// prepend learner, dataset name, auc window width values to results
				result_table = result_table :+ (learner +: (mawi_dataset.split("/").last +:(auc_window_width +: result_list)));
			}
		}
	}
	// final computation time
	val estimatedTime = (System.nanoTime() - startTime) / 1e9d;
	println(estimatedTime)
	
#### [ELKI](https://elki-project.github.io/)
ELKI [documentation](https://elki-project.github.io/releases/current/doc/overview-summary.html) is better than MOA, but not as good as WEKA :).
The authors of ELKI also do not advise to use ELKI in the pure java API form, this guide was created based on notes on ELKI [website](https://elki-project.github.io/howto/java_api) to serve as an example. This is also desribed in [stackoverflow response](https://stackoverflow.com/questions/15326505/running-clustering-algorithms-in-elki).

oneliner(again):
	
	java -jar elki.jar KDDCLIApplication -dbc.in "C:\\#CVUT\\AIT_internship\\MOA\\datasets\\mawi_samples\\mawi_data_1_long.arff" -time -algorithm clustering.kmeans.KMeansLloyd -kmeans.k 10 -resulthandler ClusteringVectorDumper
	
Jython code[TBD - UNFINISHED] :

	big-dama@bigdama-vworker1-phy7:~/pavol$ export CLASSPATH=$CLASSPATH:/home/big-dama/pavol/elki-bundle-0.7.2-SNAPSHOT.jar
	big-dama@bigdama-vworker1-phy7:~/pavol$ jython
	"my" variable $jythonHome masks earlier declaration in same scope at /usr/bin/jython line 15.
	*sys-package-mgr*: processing new jar, '/home/big-dama/pavol/elki-bundle-0.7.2-SNAPSHOT.jar'
	Jython 2.5.3 (, Sep 21 2017, 03:12:48)
	[Java HotSpot(TM) 64-Bit Server VM (Oracle Corporation)] on java1.8.0_171
	Type "help", "copyright", "credits" or "license" for more information.
	>>>
	>>> import de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.initialization.RandomlyGeneratedInitialMeans as rand_gen_means
	>>> import de.lmu.ifi.dbs.elki.utilities.random.RandomFactory as RandomFactory
	>>> dir(RandomFactory)
	['DEFAULT', '__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'class', 'equals', 'get', 'getClass', 'getRandom', 'getSingleThreadedRandom', 'hashCode', 'notify', 'notifyAll', 'random', 'singleThreadedRandom', 'toString', 'wait']
	>>> init = rand_gen_means(RandomFactory.DEFAULT)
	>>> dir(euclid)
	['Parameterizer', 'STATIC', '__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'class', 'dimensionality', 'distance', 'equals', 'getClass', 'getInputTypeRestriction', 'hashCode', 'inputTypeRestriction', 'instantiate', 'isMetric', 'isSquared', 'isSymmetric', 'metric', 'minDist', 'norm', 'notify', 'notifyAll', 'squared', 'symmetric', 'toString', 'wait']
	>>> distance = euclid.STATIC
	>>>
	>>> import de.lmu.ifi.dbs.elki.distance.distancefunction.minkowski.SquaredEuclideanDistanceFunction as euclid
	>>> dir(euclid)
	['Parameterizer', 'STATIC', '__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'class', 'dimensionality', 'distance', 'equals', 'getClass', 'getInputTypeRestriction', 'hashCode', 'inputTypeRestriction', 'instantiate', 'isMetric', 'isSquared', 'isSymmetric', 'metric', 'minDist', 'norm', 'notify', 'notifyAll', 'squared', 'symmetric', 'toString', 'wait']
	>>> distance = euclid.STATIC
	>>>
	>>> import de.lmu.ifi.dbs.elki.algorithm.clustering.kmeans.KMeansLloyd as kmeans	
	>>> kmeans_algorithm = kmeans(distance,1,0,init)
	>>> 	
	>>> import de.lmu.ifi.dbs.elki.datasource.parser.ArffParser as arffparser
	>>> file = "/home/big-dama/pavol/mawi_datasets/mawi_data_1_long.arff"
	?????????????????????
	?????????????????????
	>>> dir(arffparser)
	['ARFF_COMMENT', 'ARFF_HEADER_ATTRIBUTE', 'ARFF_HEADER_DATA', 'ARFF_HEADER_RELATION', 'ARFF_NUMERIC', 'DEFAULT_ARFF_MAGIC_CLASS', 'DEFAULT_ARFF_MAGIC_EID', 'EMPTY', 'Parameterizer', '__class__', '__copy__', '__deepcopy__', '__delattr__', '__doc__', '__eq__', '__getattribute__', '__hash__', '__init__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__str__', '__unicode__', 'class', 'cleanup', 'equals', 'getClass', 'hashCode', 'notify', 'notifyAll', 'parse', 'toString', 'wait']
	>>> file = "/home/big-dama/pavol/mawi_datasets/mawi_data_1_long.arff"
	>>> data = arffparser(file)
	Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
	TypeError: de.lmu.ifi.dbs.elki.datasource.parser.ArffParser(): expected 2 args; got 1
	>>> data = arffparser(file,-1)
	Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
	TypeError: de.lmu.ifi.dbs.elki.datasource.parser.ArffParser(): 1st arg can't be coerced to java.util.regex.Pattern, String
	>>> data = arffparser()
	Traceback (most recent call last):
	File "<stdin>", line 1, in <module>
	TypeError: de.lmu.ifi.dbs.elki.datasource.parser.ArffParser(): expected 2 args; got 0
	>>> data = arffparser
	>>> data.DEFAULT_ARFF_MAGIC_CLASS
	u'(Class|Class-?Label)'
	>>> data.DEFAULT_ARFF_MAGIC_EID
	u'(External-?ID)'
	>>>

	

	
# **USE CASE** : Run java-based tool(MOA) with multiple parallel tasks[parallel computing model]:
Usefull for multiple intensive computations/large range of algorithm parameters for analysis.


## Howto:
We will implement evaluation presented in *SIGCOMM'18 Workshop on Big Data Analytics and Machine Learning for Data Communication Networks (Big-DAMA 2018) "Stream-based Machine Learning for Network Security and Anomaly Detection"* paper. Remaining algorithms can be easily added by addding/copypasting knn methods in *"MOA + Jython UDF"*.
1. We will transform Jython interactive shell commands from previous section to User Defined Functions(UDFs). Each function includes [decorator](https://www.programiz.com/python-programming/decorator) "@outputSchema".
The decorator is not important for standalone use but Apache Pig needs it to handle the output of the UDF.
2. get the dataset into distributed cache as Apache Pig is able to work with local files only in local - non-cluster mode and MOA needs an .arff file as a data source
3. call the functions from the Apache Pig by Pig Latin commands

### Compress/pack and copy dataset files to hdfs:

	# tar gunzip the files
	# what switches mean: 
	#    -c: Create an archive.
	#    -z: Compress the archive with gzip.
	#    -v: Display progress in the terminal while creating the archive, also known as “verbose” mode. The v is always optional in these commands, but it’s helpful.
	#    -f: Allows you to specify the filename of the archive.
	sudo tar -czvf mawi_data_long.tar.gz ./mawi*
	# copy the file to hdfs location
	hadoop fs -copyFromLocal mawi_data_long.tar.gz /user/big-dama/pavol/mawi_data_long.tar.gz
	
### MOA + Jython UDF
	#/usr/bin/env python
	
	import moa.streams.ArffFileStream as arff_stream
	import moa.tasks.EvaluatePrequentialCV as evaluate
	import moa.evaluation.AdwinClassificationPerformanceEvaluator as adwin
	import moa.evaluation.WindowAUCImbalancedPerformanceEvaluator as auc
	import moa.classifiers.lazy.kNN as knn
	import moa.classifiers.functions.SGD as sgd
	import moa.classifiers.meta.AdaptiveRandomForest as arf
	import moa.classifiers.trees.HoeffdingTree as hat
	import java.io.IOException

	def tenCV_adwin_evaluation(input_file_location):
        stream = arff_stream(input_file_location,-1)
        stream.prepareForUse()
        evaluation = evaluate()
        evaluation.streamOption.setCurrentObject(stream)
        evaluation_opt = adwin()
        evaluation.evaluatorOption.setCurrentObject(evaluation_opt)
        # as we are looking only at the final time we dont need the evaluation after every 'othe sample'
        #samplefrequency = 2
        #evaluation.sampleFrequencyOption.setValue(samplefrequency)
        return evaluation

	def auc_evaluation(input_file_location,auc_window):
        stream = arff_stream(input_file_location,-1)
        stream.prepareForUse()
        evaluation = evaluate()
        evaluation.streamOption.setCurrentObject(stream)
        evaluation_opt = auc()
        evaluation_opt.widthOption.setValue(auc_window)
        evaluation.evaluatorOption.setCurrentObject(evaluation_opt)
        # as we are looking only at the final time we dont need the evaluation after every 'othe sample'
        #samplefrequency = 1
        #evaluation.sampleFrequencyOption.setValue(samplefrequency)
        return evaluation

	@outputSchema("results:chararray")
	def knn_adwin_eval(input_file_location):
        evaluation = tenCV_adwin_evaluation(input_file_location)
        learner = knn()
        evaluation.learnerOption.setCurrentObject(learner)
        evaluation.prepareForUse()
        # possible include only evaluation time (cpu seconds)
        # return str(results).split(',')[13]
        dataset = input_file_location.split("/")[-1].split(".")[0]
        auc_window_width = "NA"
        learner = "knn"
        return (learner + "," +  dataset + "," + auc_window_width + "," + str(evaluation.doTask()).split("\n")[1])

	@outputSchema("results:chararray")
	def knn_auc_eval(input_file_location,auc_window):
        evaluation = auc_evaluation(input_file_location, auc_window)
        learner = knn()
        evaluation.learnerOption.setCurrentObject(learner)
        evaluation.prepareForUse()
        dataset = input_file_location.split("/")[-1].split(".")[0]
        auc_window_width = str(auc_window)
        learner = "knn"
        return (learner + "," + dataset + "," + auc_window_width + "," + str(evaluation.doTask()).split("\n")[1])
		
### Apache Pig:
content of mawi_dataset_files.pig and auc_window_width.pig used in the script:

	big-dama@bigdama-vworker1-phy7:~/pavol/INFOCOMM18$ hdfs dfs -cat /user/big-dama/pavol/mawi_dataset_files.pig
	mawi_datasets/mawi_data_1_long.arff
	mawi_datasets/mawi_data_2_long.arff
	mawi_datasets/mawi_data_3_long.arff
	mawi_datasets/mawi_data_4_long.arff
	mawi_datasets/mawi_data_5_long.arff
	big-dama@bigdama-vworker1-phy7:~/pavol/INFOCOMM18$ hdfs dfs -cat /user/big-dama/pavol/auc_window_width.pig
	mawi_datasets/mawi_data_1_long.arff,270
	mawi_datasets/mawi_data_1_long.arff,540
	mawi_datasets/mawi_data_1_long.arff,810
	mawi_datasets/mawi_data_1_long.arff,1080
	mawi_datasets/mawi_data_1_long.arff,1350
	mawi_datasets/mawi_data_1_long.arff,1620
	mawi_datasets/mawi_data_1_long.arff,1890
	mawi_datasets/mawi_data_1_long.arff,2160
	mawi_datasets/mawi_data_1_long.arff,2430
	mawi_datasets/mawi_data_1_long.arff,2700
	mawi_datasets/mawi_data_2_long.arff,270
	mawi_datasets/mawi_data_2_long.arff,540
	mawi_datasets/mawi_data_2_long.arff,810
	mawi_datasets/mawi_data_2_long.arff,1080
	mawi_datasets/mawi_data_2_long.arff,1350
	mawi_datasets/mawi_data_2_long.arff,1620
	mawi_datasets/mawi_data_2_long.arff,1890
	mawi_datasets/mawi_data_2_long.arff,2160
	mawi_datasets/mawi_data_2_long.arff,2430
	mawi_datasets/mawi_data_2_long.arff,2700
	mawi_datasets/mawi_data_3_long.arff,270
	mawi_datasets/mawi_data_3_long.arff,540
	mawi_datasets/mawi_data_3_long.arff,810
	mawi_datasets/mawi_data_3_long.arff,1080
	mawi_datasets/mawi_data_3_long.arff,1350
	mawi_datasets/mawi_data_3_long.arff,1620
	mawi_datasets/mawi_data_3_long.arff,1890
	mawi_datasets/mawi_data_3_long.arff,2160
	mawi_datasets/mawi_data_3_long.arff,2430
	mawi_datasets/mawi_data_3_long.arff,2700
	mawi_datasets/mawi_data_4_long.arff,270
	mawi_datasets/mawi_data_4_long.arff,540
	mawi_datasets/mawi_data_4_long.arff,810
	mawi_datasets/mawi_data_4_long.arff,1080
	mawi_datasets/mawi_data_4_long.arff,1350
	mawi_datasets/mawi_data_4_long.arff,1620
	mawi_datasets/mawi_data_4_long.arff,1890
	mawi_datasets/mawi_data_4_long.arff,2160
	mawi_datasets/mawi_data_4_long.arff,2430
	mawi_datasets/mawi_data_4_long.arff,2700
	mawi_datasets/mawi_data_5_long.arff,270
	mawi_datasets/mawi_data_5_long.arff,540
	mawi_datasets/mawi_data_5_long.arff,810
	mawi_datasets/mawi_data_5_long.arff,1080
	mawi_datasets/mawi_data_5_long.arff,1350
	mawi_datasets/mawi_data_5_long.arff,1620
	mawi_datasets/mawi_data_5_long.arff,1890
	mawi_datasets/mawi_data_5_long.arff,2160
	mawi_datasets/mawi_data_5_long.arff,2430
	mawi_datasets/mawi_data_5_long.arff,2700

code:
	
	--this creates a directory symlink "mawi_datasets" to unpacked hdfs:///user/big-dama/pavol/mawi_data_long.tar.gz on each yarn node -> each yarn node will be able to access .arff dataset files as local files(MOA is unable to read hdfs files)
	--more information regarding the apache pig distributed cache can be find in the following blog post : https://ragrawal.wordpress.com/2014/03/25/apache-pig-and-distributed-cache/
	SET mapred.createsymlink YES;
	SET mapred.cache.archives hdfs:///user/big-dama/pavol/mawi_data_long.tar.gz#mawi_datasets;
	--register MOA library and Jython UDF
	REGISTER '/home/big-dama/pavol/moa-release-2018.6.0/lib/moa.jar'
	REGISTER 'infocomm18_eval.jy' USING jython AS eval_udf;
	
	--OPTION 1[START] - this results in 2 mapreduce jobs, in order to create more jobs you have to have more input files
	mawi_dataset = LOAD '/user/big-dama/pavol/mawi_dataset_files.pig' AS (input_file:chararray);
	auc_window_width_list = LOAD '/user/big-dama/pavol/auc_window_width.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	
	--PARALLEL keyword could be used to force parallelization of the computations for small input files, unfortunatelly it doesn't seem to work for a files with 5-50 entries :(
	results_knn_adwin = FOREACH mawi_dataset GENERATE eval_udf.knn_adwin_eval(input_file);
	results_knn_auc = FOREACH auc_window_width_list GENERATE eval_udf.knn_auc_eval(input_file,auc_width);

	--"With multi-query exection, you want to use STORE to save (persist) your results. You do not want to use DUMP as it will disable multi-query execution and is likely to slow down execution. (If you have included DUMP statements in your scripts for debugging purposes, you should remove them.)"
	--https://pig.apache.org/docs/r0.14.0/perf.html#multi-query-execution
	--
	--DUMP results_knn_adwin;
	--DUMP results_knn_auc;

	STORE results_knn_adwin INTO 'results_knn_adwin';
	STORE results_knn_auc INTO 'results_knn_auc';
	-- OPTION 1[END]
	
	--OPTION 2[START] - name of each input file or combination of input file and AUC parameter is stored in separate file, thise creates 54 jobs
	mawi_dataset1 = LOAD '/user/big-dama/pavol/separate_files/1.pig' AS (input_file:chararray);
	mawi_dataset2 = LOAD '/user/big-dama/pavol/separate_files/2.pig' AS (input_file:chararray);
	mawi_dataset3 = LOAD '/user/big-dama/pavol/separate_files/3.pig' AS (input_file:chararray);
	mawi_dataset4 = LOAD '/user/big-dama/pavol/separate_files/4.pig' AS (input_file:chararray);
	mawi_dataset5 = LOAD '/user/big-dama/pavol/separate_files/5.pig' AS (input_file:chararray);
	auc_window_width_list1_1 = LOAD '/user/big-dama/pavol/separate_files/1_1.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_2 = LOAD '/user/big-dama/pavol/separate_files/1_2.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_3 = LOAD '/user/big-dama/pavol/separate_files/1_3.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_4 = LOAD '/user/big-dama/pavol/separate_files/1_4.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_5 = LOAD '/user/big-dama/pavol/separate_files/1_5.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_6 = LOAD '/user/big-dama/pavol/separate_files/1_6.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_7 = LOAD '/user/big-dama/pavol/separate_files/1_7.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_8 = LOAD '/user/big-dama/pavol/separate_files/1_8.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_9 = LOAD '/user/big-dama/pavol/separate_files/1_9.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list1_10 = LOAD '/user/big-dama/pavol/separate_files/1_10.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_1 = LOAD '/user/big-dama/pavol/separate_files/2_1.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_2 = LOAD '/user/big-dama/pavol/separate_files/2_2.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_3 = LOAD '/user/big-dama/pavol/separate_files/2_3.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_4 = LOAD '/user/big-dama/pavol/separate_files/2_4.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_5 = LOAD '/user/big-dama/pavol/separate_files/2_5.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_6 = LOAD '/user/big-dama/pavol/separate_files/2_6.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_7 = LOAD '/user/big-dama/pavol/separate_files/2_7.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_8 = LOAD '/user/big-dama/pavol/separate_files/2_8.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_9 = LOAD '/user/big-dama/pavol/separate_files/2_9.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list2_10 = LOAD '/user/big-dama/pavol/separate_files/2_10.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_1 = LOAD '/user/big-dama/pavol/separate_files/3_1.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_2 = LOAD '/user/big-dama/pavol/separate_files/3_2.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_3 = LOAD '/user/big-dama/pavol/separate_files/3_3.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_4 = LOAD '/user/big-dama/pavol/separate_files/3_4.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_5 = LOAD '/user/big-dama/pavol/separate_files/3_5.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_6 = LOAD '/user/big-dama/pavol/separate_files/3_6.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_7 = LOAD '/user/big-dama/pavol/separate_files/3_7.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_8 = LOAD '/user/big-dama/pavol/separate_files/3_8.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_9 = LOAD '/user/big-dama/pavol/separate_files/3_9.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list3_10 = LOAD '/user/big-dama/pavol/separate_files/3_10.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_1 = LOAD '/user/big-dama/pavol/separate_files/4_1.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_2 = LOAD '/user/big-dama/pavol/separate_files/4_2.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_3 = LOAD '/user/big-dama/pavol/separate_files/4_3.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_4 = LOAD '/user/big-dama/pavol/separate_files/4_4.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_5 = LOAD '/user/big-dama/pavol/separate_files/4_5.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_6 = LOAD '/user/big-dama/pavol/separate_files/4_6.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_7 = LOAD '/user/big-dama/pavol/separate_files/4_7.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_8 = LOAD '/user/big-dama/pavol/separate_files/4_8.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_9 = LOAD '/user/big-dama/pavol/separate_files/4_9.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list4_10 = LOAD '/user/big-dama/pavol/separate_files/4_10.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_1 = LOAD '/user/big-dama/pavol/separate_files/5_1.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_2 = LOAD '/user/big-dama/pavol/separate_files/5_2.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_3 = LOAD '/user/big-dama/pavol/separate_files/5_3.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_4 = LOAD '/user/big-dama/pavol/separate_files/5_4.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_5 = LOAD '/user/big-dama/pavol/separate_files/5_5.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_6 = LOAD '/user/big-dama/pavol/separate_files/5_6.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_7 = LOAD '/user/big-dama/pavol/separate_files/5_7.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_8 = LOAD '/user/big-dama/pavol/separate_files/5_8.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_9 = LOAD '/user/big-dama/pavol/separate_files/5_9.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);
	auc_window_width_list5_10 = LOAD '/user/big-dama/pavol/separate_files/5_10.pig' using PigStorage(',') AS (input_file:chararray, auc_width:int);

	results_knn_adwin1_1 = FOREACH auc_window_width_list1_1 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_2 = FOREACH auc_window_width_list1_2 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_3 = FOREACH auc_window_width_list1_3 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_4 = FOREACH auc_window_width_list1_4 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_5 = FOREACH auc_window_width_list1_5 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_6 = FOREACH auc_window_width_list1_6 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_7 = FOREACH auc_window_width_list1_7 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_8 = FOREACH auc_window_width_list1_8 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_9 = FOREACH auc_window_width_list1_9 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin1_10 = FOREACH auc_window_width_list1_10 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_1 = FOREACH auc_window_width_list2_1 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_2 = FOREACH auc_window_width_list2_2 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_3 = FOREACH auc_window_width_list2_3 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_4 = FOREACH auc_window_width_list2_4 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_5 = FOREACH auc_window_width_list2_5 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_6 = FOREACH auc_window_width_list2_6 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_7 = FOREACH auc_window_width_list2_7 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_8 = FOREACH auc_window_width_list2_8 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_9 = FOREACH auc_window_width_list2_9 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin2_10 = FOREACH auc_window_width_list2_10 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_1 = FOREACH auc_window_width_list3_1 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_2 = FOREACH auc_window_width_list3_2 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_3 = FOREACH auc_window_width_list3_3 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_4 = FOREACH auc_window_width_list3_4 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_5 = FOREACH auc_window_width_list3_5 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_6 = FOREACH auc_window_width_list3_6 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_7 = FOREACH auc_window_width_list3_7 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_8 = FOREACH auc_window_width_list3_8 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_9 = FOREACH auc_window_width_list3_9 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin3_10 = FOREACH auc_window_width_list3_10 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_1 = FOREACH auc_window_width_list4_1 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_2 = FOREACH auc_window_width_list4_2 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_3 = FOREACH auc_window_width_list4_3 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_4 = FOREACH auc_window_width_list4_4 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_5 = FOREACH auc_window_width_list4_5 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_6 = FOREACH auc_window_width_list4_6 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_7 = FOREACH auc_window_width_list4_7 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_8 = FOREACH auc_window_width_list4_8 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_9 = FOREACH auc_window_width_list4_9 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin4_10 = FOREACH auc_window_width_list4_10 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_1 = FOREACH auc_window_width_list5_1 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_2 = FOREACH auc_window_width_list5_2 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_3 = FOREACH auc_window_width_list5_3 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_4 = FOREACH auc_window_width_list5_4 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_5 = FOREACH auc_window_width_list5_5 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_6 = FOREACH auc_window_width_list5_6 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_7 = FOREACH auc_window_width_list5_7 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_8 = FOREACH auc_window_width_list5_8 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_9 = FOREACH auc_window_width_list5_9 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);
	results_knn_adwin5_10 = FOREACH auc_window_width_list5_10 GENERATE eval_udf.knn_auc_eval(input_file,auc_width);

	STORE results_knn_adwin1 INTO 'pavol/separate_results/results_knn_adwin1';
	STORE results_knn_adwin2 INTO 'pavol/separate_results/results_knn_adwin2';
	STORE results_knn_adwin3 INTO 'pavol/separate_results/results_knn_adwin3';
	STORE results_knn_adwin4 INTO 'pavol/separate_results/results_knn_adwin4';
	STORE results_knn_adwin5 INTO 'pavol/separate_results/results_knn_adwin5';
	STORE results_knn_adwin1_1 INTO 'pavol/separate_results/results_knn_adwin1_1';
	STORE results_knn_adwin1_2 INTO 'pavol/separate_results/results_knn_adwin1_2';
	STORE results_knn_adwin1_3 INTO 'pavol/separate_results/results_knn_adwin1_3';
	STORE results_knn_adwin1_4 INTO 'pavol/separate_results/results_knn_adwin1_4';
	STORE results_knn_adwin1_5 INTO 'pavol/separate_results/results_knn_adwin1_5';
	STORE results_knn_adwin1_6 INTO 'pavol/separate_results/results_knn_adwin1_6';
	STORE results_knn_adwin1_7 INTO 'pavol/separate_results/results_knn_adwin1_7';
	STORE results_knn_adwin1_8 INTO 'pavol/separate_results/results_knn_adwin1_8';
	STORE results_knn_adwin1_9 INTO 'pavol/separate_results/results_knn_adwin1_9';
	STORE results_knn_adwin1_10 INTO 'pavol/separate_results/results_knn_adwin1_10';
	STORE results_knn_adwin2_1 INTO 'pavol/separate_results/results_knn_adwin2_1';
	STORE results_knn_adwin2_2 INTO 'pavol/separate_results/results_knn_adwin2_2';
	STORE results_knn_adwin2_3 INTO 'pavol/separate_results/results_knn_adwin2_3';
	STORE results_knn_adwin2_4 INTO 'pavol/separate_results/results_knn_adwin2_4';
	STORE results_knn_adwin2_5 INTO 'pavol/separate_results/results_knn_adwin2_5';
	STORE results_knn_adwin2_6 INTO 'pavol/separate_results/results_knn_adwin2_6';
	STORE results_knn_adwin2_7 INTO 'pavol/separate_results/results_knn_adwin2_7';
	STORE results_knn_adwin2_8 INTO 'pavol/separate_results/results_knn_adwin2_8';
	STORE results_knn_adwin2_9 INTO 'pavol/separate_results/results_knn_adwin2_9';
	STORE results_knn_adwin2_10 INTO 'pavol/separate_results/results_knn_adwin2_10';
	STORE results_knn_adwin3_1 INTO 'pavol/separate_results/results_knn_adwin3_1';
	STORE results_knn_adwin3_2 INTO 'pavol/separate_results/results_knn_adwin3_2';
	STORE results_knn_adwin3_3 INTO 'pavol/separate_results/results_knn_adwin3_3';
	STORE results_knn_adwin3_4 INTO 'pavol/separate_results/results_knn_adwin3_4';
	STORE results_knn_adwin3_5 INTO 'pavol/separate_results/results_knn_adwin3_5';
	STORE results_knn_adwin3_6 INTO 'pavol/separate_results/results_knn_adwin3_6';
	STORE results_knn_adwin3_7 INTO 'pavol/separate_results/results_knn_adwin3_7';
	STORE results_knn_adwin3_8 INTO 'pavol/separate_results/results_knn_adwin3_8';
	STORE results_knn_adwin3_9 INTO 'pavol/separate_results/results_knn_adwin3_9';
	STORE results_knn_adwin3_10 INTO 'pavol/separate_results/results_knn_adwin3_10';
	STORE results_knn_adwin4_1 INTO 'pavol/separate_results/results_knn_adwin4_1';
	STORE results_knn_adwin4_2 INTO 'pavol/separate_results/results_knn_adwin4_2';
	STORE results_knn_adwin4_3 INTO 'pavol/separate_results/results_knn_adwin4_3';
	STORE results_knn_adwin4_4 INTO 'pavol/separate_results/results_knn_adwin4_4';
	STORE results_knn_adwin4_5 INTO 'pavol/separate_results/results_knn_adwin4_5';
	STORE results_knn_adwin4_6 INTO 'pavol/separate_results/results_knn_adwin4_6';
	STORE results_knn_adwin4_7 INTO 'pavol/separate_results/results_knn_adwin4_7';
	STORE results_knn_adwin4_8 INTO 'pavol/separate_results/results_knn_adwin4_8';
	STORE results_knn_adwin4_9 INTO 'pavol/separate_results/results_knn_adwin4_9';
	STORE results_knn_adwin4_10 INTO 'pavol/separate_results/results_knn_adwin4_10';
	STORE results_knn_adwin5_1 INTO 'pavol/separate_results/results_knn_adwin5_1';
	STORE results_knn_adwin5_2 INTO 'pavol/separate_results/results_knn_adwin5_2';
	STORE results_knn_adwin5_3 INTO 'pavol/separate_results/results_knn_adwin5_3';
	STORE results_knn_adwin5_4 INTO 'pavol/separate_results/results_knn_adwin5_4';
	STORE results_knn_adwin5_5 INTO 'pavol/separate_results/results_knn_adwin5_5';
	STORE results_knn_adwin5_6 INTO 'pavol/separate_results/results_knn_adwin5_6';
	STORE results_knn_adwin5_7 INTO 'pavol/separate_results/results_knn_adwin5_7';
	STORE results_knn_adwin5_8 INTO 'pavol/separate_results/results_knn_adwin5_8';
	STORE results_knn_adwin5_9 INTO 'pavol/separate_results/results_knn_adwin5_9';
	STORE results_knn_adwin5_10 INTO 'pavol/separate_results/results_knn_adwin5_10';