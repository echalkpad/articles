# Research paper categorization using machine learning and NLP

[Original URL](http://aqibsaeed.github.io/2016-07-26-text-classification/?cmp=em-data-na-na-newsltr_ai_20160808)

> Text classification (a.k.a. text categorization) is one of the most prominent application of Machine Learning. It is used to automatically assign predefined categories (labels) to free-text...

Text classification (a.k.a. text categorization) is one of the most prominent application of Machine Learning. It is used to automatically assign predefined categories (labels) to free-text documents. The purpose of text classification is to give conceptual organization to large collection of documents. It has become more relevant with exponential growth of the data, with wide applicability in real world applications e.g. categorization of news stories by topics, products classification into multilevel categories, organization of emails into various groups (such as social, primary and promotions), indexing of patients data in healthcare sector from multiple aspects e.g. disease type, surgical procedure and medication given etc.

An interesting application of text classification is to categorize research papers by most suitable conferences. Finding and selecting a suitable academic conference has always been a challenging task especially for young researchers. We can define a 'suitable academic conference' as a conference, which is aligned with the researcher's work and have a good academic ranking. Usually researcher have to consult to their supervisors and search extensively to find a suitable conference. Among many conferences, few are considered to be relevant to send a research work. To fulfil editorial and content specific demands of conferences, researcher needs to go through the previously published proceedings of a certain conference. Based on previous proceeding of a conferences, the research work is sometimes modified to increase the chances of article acceptances and publication. This problem can be solved to some extent using machine learning techniques e.g. classification algorithms like SVM, Naïve Bayes, etc.

Thus, the objective of this tutorial is to provide hands on experience on how to perform text classification using conference proceedings dataset. We will learn how to apply various classification algorithms to categorize research papers by conferences along with feature selection and dimensionality reduction methods using popular [scikit-learn](http://aqibsaeed.github.io/2016-07-26-text-classification/”) library in Python.

Let's import the required libraries, which we will use to process the text, performing feature selection, model training and evaluation.

The dataset consists of 2507 short research paper titles, largely technology related. They have been pre classified manually into 5 categories (by conferences). Figure 1, summarizes the distribution of research papers by different conferences.

![Dataset distribution](http://aqibsaeed.github.io/img/tc-dataset-distribution.png)

`loadData` method will be used to read the dataset from text file and return titles and conference names. Similarly, `preProcessing` method will take titles as parameter and will perform text pre-processing steps. By iterating over titles it will convert the text into lowercase and remove the stop words (that are provided in corpus of NLTK library). You can uncomment one line code in this method, if you wish to remove punctuation and numbers from the titles. Few research paper titles and corresponding conferences are shown in Figure 2.

![Paper titles and conferences](http://aqibsaeed.github.io/img/tc-dataset-sample.png)

Machine learning algorithms requires numeric feature vectors to learn the underlying representation of the dataset. For this purpose we need to convert our text into some numeric form. There are couple of different ways to do this. We will use Term frequency-inverse document frequency or TF-IDF. It is the measure of how important the term is, for a particular document in a corpus. If the term is frequent in the document and appear less frequently in the corpus, then term is of high importance for the document. It is computed by the product of two terms as follows: the first computes the term frequency (tf), aka. Number of times a word appears in a document, divided by the total number of words in that document; the second term is the inverse document frequency (idf), computed as the logarithm of the number of the documents in the corpus divided by the number of documents where the specific term appears. Hence, terms in the document with a high tf-idf score occur frequently in the document and provide the most information about that specific document.

`getDTMByTFIDF` method takes two parameters pre-processed title and number of (top) maximum feature that we want to get from corpus. If `None` is passed as `nfeatures`, the method will return all the terms. `TfidfVectorizer` takes several other parameters to learn more about this consult the [documentation](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html). This method also return the `tfIdf_vectorizer` object in addition to document term matrix. The vectorizer object can later be used to transform test set (unseen/new) titles during prediction.

The document term matrix (from Tf-Idf) is usually very high dimensional and sparse. It can create issues for machine learning algorithms during learning phase. Therefore, it is recommended to reduce the dimensionality of the dataset by either feature selection or dimensionality reduction methods. The former selects important features from the original feature set whereas, later learns new features from the original set in some other dimension. We will apply Chi Square and Information Gain as feature selection methods and Latent Semantic Analysis as dimensionality reduction technique.

The χ2 test is used in statistics to test the independence of two events. More precisely in feature selection it is used to test whether the occurrence of a specific term and the occurrence of a specific class are independent. `featuresByChiSq` method takes titles and corresponding labels along with number of top features we want to get based on their importance.

Information gain is another technique to determine the relative importance of the term. It measures the number of bits required for category prediction by knowing the presence or the absence of a term in the document. `featuresByInformationGain` method uses decision tree and entropy criteria to learn discriminative features.

LSA is an unsupervised statistical technique used for extracting and inferring relations of expected contextual usage of words in documents. It utilizes singular value decomposition (SVD) to reduce the high dimensionality of text data. It does so by keeping the first k largest singular values and omitting the rest. `featuresByLSA` method takes titles and number of components (features) that we want LSA to learn from original features.

We will use three classification algorithms to categorize research papers. 1) Support vector machine with linear kernel 2) Random forest and 3) Multinomial Naïve Bayes. For evaluating/comparing how well the models are performing stratified k-fold cross validation will be used. It seeks to ensure that each fold is a good representative of whole. Generally, this is used to evaluate performance of supervised learning algorithms and aims to ensure, each class has (approximately) equal representation across each fold. Weighted average precision, recall and f-score will be calculated that can be used for final model selection. `crossValidate` method will perform above discussed steps of model training and evaluation. It takes document term matrix, class labels, classifier name and number of folds for cross validation and returns averaged precision, recall and f-score.

We came a long way and have all the required pieces to achieve the goal of research papers categorization by conferences. Now we just need to call above defined methods in the order shown by Figure 3, it shows typical steps of machine learning pipeline. These steps should be performed systematically to see which techniques/classifiers are working well.

![Machine learning pipeline](http://aqibsaeed.github.io/img/tc-ml-pipiline.png)

Results achieved by executing above code are shown below in Table 1\. Default hyper parameters values are used for all the classifiers. I would recommend to try different text processing techniques, number of features, optimize hyper parameter values and even use some different classification algorithm.

![Classification results](http://aqibsaeed.github.io/img/tc-results.png)

In this tutorial, we evaluated performance of different classifiers using 10-fold stratified cross validation technique after applying text pre-processing, feature selection along with dimensionality reduction methods for text classification problem, which is to determine most suitable conference for a research paper based on its title. If you want to learn more about these methods, please consult the papers mentioned below.

Python notebook and dataset for this blog post is available at this[**link**](https://github.com/aqibsaeed/Text-Classification).

- _Sebastiani, F., 2002\. Machine learning in automated text categorization. ACM computing surveys (CSUR), 34(1), pp.1–47._
- _Yang, Y. & Pedersen, J.O., 1997\. A comparative study on feature selection in text categorization. In ICML. pp. 412–420._
- _Kohavi, R. & others, 1995\. A study of cross-validation and bootstrap for accuracy estimation and model selection. In Ijcai. pp. 1137–1145._
- _Deerwester, S. et al., 1990\. Indexing by latent semantic analysis. Journal of the American society for information science, 41(6), p.391._
