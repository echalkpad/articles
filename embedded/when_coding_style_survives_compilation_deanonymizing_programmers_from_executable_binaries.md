# When coding style survives compilation: De-anonymizing programmers from executable binaries

[Original URL](https://freedom-to-tinker.com/blog/aylin/when-coding-style-survives-compilation-de-anonymizing-programmers-from-executable-binaries/)

> In a recent paper, we showed that coding style is present in source code and can be used to de-anonymize programmers. But what if only compiled binaries are available, rather than source code? Today...

<span>In a <a href="http://www.princeton.edu/~aylinc/papers/caliskan-islam_deanonymizing.pdf">recent paper</a>, we showed that coding style is present in source code and can be used to de-anonymize programmers. But what if only compiled binaries are available, rather than source code? Today we are releasing a <a href="http://www.princeton.edu/~aylinc/papers/caliskan-islam_when.pdf">new paper</a> showing that coding style can survive compilation. Consequently, we can utilize these stylistic fingerprints via machine learning and de-anonymize programmers of executable binaries with high accuracy. This finding is of concern for privacy-aware programmers who would like to remain anonymous.</span>

_Update: [Video of the talk](https://media.ccc.de/v/32c3-7491-de-anonymizing_programmers#video) at the Chaos Communication Congress_

**_How to represent the coding style in an executable binary._**

<span>Executable binaries of compiled source code on their own are difficult to analyze because they lack human readable information. Nevertheless reverse engineering methods make it possible to disassemble and decompile executable binaries. After applying such reverse engineering methods to executable binaries, we can generate numeric representations of authorial style from features preserved in binaries. </span>

<span>We use a dataset consisting of source code samples of 600 programmers which are available on the website of the annual programming competition Google Code Jam (GCJ). This dataset contains information such as how many rounds the contestants were able to advance, inferring their programming skill, while all contestants were implementing algorithmic solutions to the same programming tasks. Since all the contestants implement the same functionality, the main difference between their samples is their coding style. Such ground truth provides a controlled environment to analyze coding style.</span>

<span>We compile the source code samples of GCJ programmers to executable binaries. We disassemble these binaries to obtain their assembly instructions. We also decompile the binaries to generate approximations of the original source code and the respective control flow graphs. We subsequently parse the decompiled code using a fuzzy parser to obtain abstract syntax trees from which structural properties of code can be derived.</span>

<span>These data sources provide a combination of high level program flow information as well as low level assembly instructions and structural syntactic properties. We convert these properties to numeric values to represent the prevalent coding style in each executable binary.</span>

**_We cast programmer de-anonymization as a machine learning problem._**

<span>We apply the machine learning workflow depicted in the figure below to de-anonymize programmers. There are three main stages in our programmer de-anonymization method. First, we extract a large variety of features from each executable binary to represent coding style with feature vectors, which is possible after applying state-of-the-art reverse engineering methods. Second, we train a random forest classifier on these vectors to generate accurate author models. Third, the random forest attributes authorship to the vectorial representations of previously unseen executable binaries. </span>

<span>The contribution of our work is that we show coding style is embedded in executable binaries as a fingerprint, making de-anonymization possible. The novelty of our method is that our feature set incorporates reverse engineering methods to generate a rich and accurate representation of properties hidden in binaries.</span>

![](http://www.princeton.edu/~aylinc/files/workFlow.png)

**_Results_**

<span>.</span>

<span>We are able to de-anonymize executable binaries of 20 programmers with 96% correct classification accuracy. In the de-anonymization process, the machine learning classifier trains on 8 executable binaries for each programmer to generate numeric representations of their coding styles. Such a high accuracy with this small amount of training data has not been reached in previous attempts. After scaling up the approach by increasing the dataset size, we de-anonymize 600 programmers with 52% accuracy. There has been no previous attempt to de-anonymize such a large binary dataset. The abovementioned executable binaries are compiled without any compiler optimizations, which are options to make binaries smaller and faster while transforming the source code more than plain compilation. As a result, compiler optimizations further normalize authorial style. For the first time in programmer de-anonymization, we show that we can still identify programmers of optimized executable binaries. While we can de-anonymize 100 programmers from unoptimized executable binaries with 78% accuracy, we can de-anonymize them from optimized executable binaries with 64% accuracy. We also show that stripping and removing symbol information from the executable binaries reduces the accuracy to 66%, which is a surprisingly small drop. This suggests that coding style survives complicated transformations.</span>

**_Other interesting contributions of the [paper](http://www.princeton.edu/~aylinc/papers/caliskan-islam_when.pdf)._**

- <span>By comparing advanced and less advanced programmers’, we found that more advanced programmers are easier to de-anonymize and they have a more distinct coding style. </span>

- <span>We also de-anonymize GitHub users in the wild, which we explain in detail in the paper. These promising results are encouraging us to extend our method to large real world datasets of various natures for future work.</span>

- <span>Why does de-anonymization work so well? It’s </span>

  _<span>not </span>_

  <span>because the decompiled source code looks anything like the original. Rather, the feature vector obtained from disassembly and decompilation can be used to </span>

  _<span>predict</span>_

  <span>, using machine learning, the features in the original source code — with over 80% accuracy. This shows that executable binaries preserve transformed versions of the original source code features. </span>

I would like to thank [Arvind Narayanan](http://randomwalker.info) for his valuable comments.
