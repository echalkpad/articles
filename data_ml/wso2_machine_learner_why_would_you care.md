# WSO2 Machine Learner: Why would You care?

[Original URL](https://iwringer.wordpress.com/2015/09/25/wso2-machine-learner-why-would-you-care/)

> After about a year worth of work, WSO2 Machine Learner (WSO2 ML) is officially out. You can find the pack from <http://wso2.com/products/machine-learner/> ( also Code and User Guide). It...

After about a year worth of work, WSO2 Machine Learner (WSO2 ML) is officially out. You can find the pack from <http://wso2.com/products/machine-learner/> ( also [Code](https://github.com/wso2/product-ml) and [User Guide](https://docs.wso2.com/display/ML100/Introducing+Machine+Learner)). It is free and Opensource under Apache Licence ( which pretty much means you can do whatever with the code as long as you keep the same Licence).

Let me try to answer "the question". How is it different and why would you care?

## What is it?

The short answer is it is a Wizard and a system on top of Apache Spark MLLib. The long answer is the following picture.

![ML-overview](https://iwringer.files.wordpress.com/2015/09/ml-overview.png?w=561&h=302)

You can use it to do the following

1. User can start with data ( in his disk, in HDFS, or in WSO2 DAS)
2. Explore the data ( more about that later)
3. Create a Project and build machine learning models going through a Wizard
4. Compare those models and find the best model
5. Export that model and use it with WSO2 CEP, WSO2 ESB, or from Java Code.

## For someone from Enterprise World?

WSO2 Machine Learner is designed for the Enterprise world. It comes as an integrated solution with the rest of the Big Data processing technologies: batch, realtime, and interactive analytics. Also, it includes support from data collection, analysis, to communication (e.g. visualizations, APIs, and alerts). Please see the earlier post "[Introducing WSO2 Analytics Platform: Note for Architects](https://iwringer.wordpress.com/2015/03/18/introducing-wso2-analytics-platform-note-for-architects/)" for more details. Hence, it is part of a complete analytics solution.

WSO2 ML handles the full predictive analytics lifecycle, including model deployment and management.

[![MLDeployment](https://iwringer.files.wordpress.com/2015/09/mldeployment.png?w=368&h=280)](https://iwringer.files.wordpress.com/2015/09/mldeployment.png)

If you are already collecting data, we can pull that data, process them, and build models. Models you built are immediately available to use from your main transaction flow ( via WSO2 ESB) or data analysis flow ( via WSO2 CEP). Basically, you copy the model ID and add it to WSO2 ESB mediation scripts or WSO2 CEP queries, and now you have a Machine Learning integrated into your business. (Please see in [Using Models](https://docs.wso2.com/display/ML100/Using+Models) for more information.) This handles details like keeping a central store of Models while deploying models in production and also let you quickly switch between models.

If you are not collecting data, you can start with WSO2 DAS and go from there. The same story holds.

Furthermore, it gives you the concept of a project where you can try out and keep track of multiple machine learning models. Also, it handles details like sending you an email when a long running machine learning algorithm execution has completed.

Finally, as we discuss in the next section, the ML Wizard is built such a way that you can use it with minimal understanding about Machine Learning. Sure, you will not get the same accuracy as the experts who will know how to tune the thing, but it can get you started and give you OK accuracy.

## For a Machine Learning Newbie?

First of all, you need to understand what Machine Learning can do for you. Most problems, we know the exact steps to be followed to solve the problem. With those kinds of problems, all we have to do is to write a code that does those steps. This is what we call programming and lot of us do this day in day out.

However, there are other problems that you will learn by example. Driving a car, cycling, and drawing a picture are problems that we learn by looking at examples. If you want a computer to solve those problems, you cannot write a program to solve them because you do not know the algorithm. Machine Learning is used to solve specifically those problems. Instead of the algorithm, you give it lots of examples, and Machine Learning will learn a model (a function) from those examples. You can use the model to solve your initial problem. Google's driverless car does exactly this.

If you are new to Machine Learning, I highly recommend looking at [A Visual Introduction to Machine Learning](http://www.r2d3.us/visual-intro-to-machine-learning-part-1/) and the following talk by Ron Beckerman.

<span class="embed-youtube">
</span>

The Machine Learner wizard tries to model the experience around what you want to do as oppose to showing you lot of ML algorithms. For example, you can choose to predict the next value, classify something to a one of the categories, or detect an anomaly. You can click through, use defaults, and get a model. You can try several algorithms and compare them with each other.

We support several standard techniques to compare ML models such as ROC curve, confusion Matrix, etc. CD's blog post "[Machine Learning for Everyone](https://cdathuraliya.wordpress.com/2015/09/17/wso2-ml-machine-learning-for-everone/)" talks about this in detail.

For example, following confusion matrix shows how much of true positives, false negatives etc resulted from the model.

![confusion-matrix_r](https://iwringer.files.wordpress.com/2015/09/confusion-matrix_r.png?w=510&h=167)

The figure on the left chart shows a scatter plot of data points that are predicted correctly and incorrectly while the right-hand side shows the RoC curve.

![predicted-vs-actual](https://iwringer.files.wordpress.com/2015/09/predicted-vs-actual.png?w=285&h=197)

![roc-graph](https://iwringer.files.wordpress.com/2015/09/roc-graph.png?w=288&h=193)

However, at this point I suggest that you read [How to Evaluate Machine Learning Models: Classification Metrics](http://blog.dato.com/how-to-evaluate-machine-learning-models-part-2a-classification-metrics) by [Alice Zheng](http://blog.dato.com/author/alice-zheng). It is ok to not to know how ML algorithms work, but you must know what models are better and why.

However, there is a catch. If you try well known Machine Learning datasets, they would work well ( You can find few of such data sets from the sample directory of the pack). However, sometimes with real datasets, getting good results need transforming features into different features, and that might be beyond you if you have just started. If you want to go pro and learn to transform features ( a.k.a. Feature Engineering) and other fascinating stuff, then Andrew Ng's famous course <https://www.coursera.org/learn/machine-learning> is the best place to start.

## For a Machine Learning Expert?

If you are an ML expert, still WSO2 Machine Learner can help in several ways.

First, it provides pretty sophisticated support for exploring the dataset based on a random sample. This includes scatter plots for looking at any two numerical features, parallel sets for looking at categorical data, Trellis sets for looking at 4-5 numerical dimensions at the same time, and cluster diagrams ( see below for some examples).

[![cluster-diagram](https://iwringer.files.wordpress.com/2015/09/cluster-diagram.png?w=302&h=200)](https://iwringer.files.wordpress.com/2015/09/cluster-diagram.png) [![trellis-chart](https://iwringer.files.wordpress.com/2015/09/trellis-chart.png?w=282&h=223)](https://iwringer.files.wordpress.com/2015/09/trellis-chart.png) [![parallel-set](https://iwringer.files.wordpress.com/2015/09/parallel-set.png?w=330&h=224)](https://iwringer.files.wordpress.com/2015/09/parallel-set.png)

Second, it gives you access to a large collection of scalable machine learning algorithms pretty easily. For a single node setup, you just download and unzip it. ( see below for how to do it).

Third, it provides an extensive set of model comparison measures as visualizations and also let you compare models side by side.

Fourth, in addition to predictive analytics, you have access to batch analytics though SparkSQL, interactive analytics with Lucence, and relatime analytics through WSO2 CEP. This will make understanding dataset as well as preprocessing data much easier. One limitation of this release is that those other types of analytics must be done before using data within WSO2 ML. However, the next release will enable you to run queries within the WSO2 ML pipeline as well.

Finally, you will also have all advantages listed under enterprise user such as seamless deployment of models and ability to switch the model easily.

Furthermore, many interesting features are coming shortly in the next release.

- Support for Deep Learning and Neural Networks
- Support for out of the Box Anomaly detection using Markov Chains and Clustering
- Support to data cleanup and preprocessing using Data Wrangler and SparkSQL
- Support for out of the box ensembles that let you combine models
- Improvements to pipeline to warn the user on cases like class imbalances in classifications

## Trying it Out

Carry out following steps

1. Download WSO2 ML from <http://wso2.com/products/machine-learner/>
2. Make sure you have Java 7 installed in your machine and set JAVA_HOME.
3. Unzip the pack and run bin/wso2server.sh from the unpacked directory. Wait for WSO2 ML to start.
4. Go to <https://hostname:9443/ml> and Login using username admin and password admin.
5. Now you can upload your own dataset and follow along with the wizard. You can find more info from the [User Guide](https://docs.wso2.com/display/ML100/Introducing+Machine+Learner). However, Wizard should be self-explanatory.

Remember, it is all free under apache Licence. Give it a try, and we will love to hear your thoughts. If you find any problems or have suggestions, report them via <https://wso2.org/jira/browse/ML>.

### Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()

### _Related_
