# Automatically Tuning Text Classifiers

[Original URL](http://blog.sigopt.com/post/133089144983/sigopt-for-ml-automatically-tuning-text)

> By: Ian Dewancker, Research EngineerIn this first post on integrating SigOpt with machine learning frameworks, we'll show you how to use SigOpt and scikit-learn to train and tune a model for...

By: [Ian Dewancker, Research Engineer](https://sigopt.com/about)

In this first post on integrating [SigOpt](https://sigopt.com/) with machine learning frameworks, we'll show you how to use SigOpt and [scikit-learn](http://scikit-learn.org/) to train and tune a model for text sentiment classification in under [50 lines of Python](https://github.com/sigopt/sigopt-examples/tree/master/text_classifier). We show that SigOpt outperforms both [strategies scikit-learn recommends](http://scikit-learn.org/stable/modules/grid_search.html) for hyperparameter optimization on this task. We'll also walk through the two fundamental aspects of this (and any) SigOpt experiment: the objective metric and the set of tunable parameters.

Text classification problems appear quite often in modern information systems, and you might imagine building a small document/tweet/blogpost classifier for any number of purposes. In this example, the classification task is to label [Amazon product reviews](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/) [1] as either favorable or not. The objective is to find a classifier that is accurate in its predictions, but also one that gives us confidence it will generalize to data it hasn't been trained on. We employ the Swiss army knife of machine learning, [logistic regression](https://en.wikipedia.org/wiki/Logistic_regression) (LR), as our model in this experiment. While the LR model might be conceptually simple and implemented in many statistics and machine learning software packages, **valuable engineering time and resources are often wasted** experimenting with feature representation and parameter tuning via trial and error. SigOpt can automatically and intelligently optimize your objective, letting you get back to working on other tasks.

## Show Me The Code

If you'd like to try running this example, [sign up](https://sigopt.com/signup) for a free SigOpt trial and find your newly created credentials on your [profile page](https://sigopt.com/user/profile). To quickly set up an environment to run this example, we recommend using a Mac OS X (with brew) or an Ubuntu machine having _at least 4GB of RAM_. Once you have a suitable machine ([t2.mediums](https://aws.amazon.com/ec2/pricing/) in EC2 are cheap and work great) this script should be all you need to get to get started:

```
# on Mac OS X or Ubuntu machine
git clone https://github.com/sigopt/sigopt-examples.git
cd sigopt-examples/text_classifier/
sudo ./setup_env.sh
# insert your CLIENT_ID, CLIENT_TOKEN, USER_TOKEN into sigopt_creds.py
# otherwise you'll see "This endpoint requires an authenticated user" errors
nohup python sentiment_classifier.py &
```

Without further ado, here is the short Python snippet to train and tune the LR sentiment classifier (you can also [find it here](https://github.com/sigopt/sigopt-examples/tree/master/text_classifier))

```
import json, math, numpy, datetime
import sigopt.interface
from sigopt_creds import USER_TOKEN, CLIENT_TOKEN, CLIENT_ID
from sklearn.feature_extraction.text import CountVectorizer
from sklearn.linear_model import SGDClassifier
from sklearn import cross_validation

#load text training data
POSITIVE_TEXT = json.load(open("POSITIVE_list.json"))
NEGATIVE_TEXT = json.load(open("NEGATIVE_list.json"))

# optimization metric
def sentiment_metric(POS_TEXT, NEG_TEXT, params):
 min_ngram = params['min_n_gram']
 max_ngram = min_ngram + params['n_gram_offset']
 min_doc_freq = math.exp(params['log_min_df'])
 max_doc_freq = min_doc_freq + params['df_offset']
 vectorizer = CountVectorizer(min_df=min_doc_freq, max_df=max_doc_freq,
 ngram_range=(min_ngram, max_ngram))
 X = vectorizer.fit_transform(POS_TEXT+NEG_TEXT)
 y = [1]*len(POS_TEXT) + [-1]*len(NEG_TEXT)
 clf = SGDClassifier(loss='log', penalty='elasticnet',
 alpha=math.exp(params['log_reg_coef']),
 l1_ratio=params['l1_coef'])
 cv = cross_validation.ShuffleSplit(X.shape[0], n_iter=5, test_size=0.3,
 random_state=0)
 cv_scores = cross_validation.cross_val_score(clf, X, y, cv=cv)
 return numpy.mean(cv_scores)

conn = sigopt.interface.Connection(user_token=USER_TOKEN,
 client_token=CLIENT_TOKEN)
experiment = conn.experiments.create(client_id=CLIENT_ID, data={
 'name': 'Sentiment LR Classifier',
 'parameters': [
 { 'name':'l1_coef',
 'type': 'double',
 'bounds': { 'min': 0, 'max': 1.0 }},
 { 'name':'log_reg_coef',
 'type': 'double',
 'bounds': { 'min': math.log(0.000001), 'max': math.log(100.0) }},
 { 'name':'min_n_gram',
 'type': 'int',
 'bounds': { 'min': 1, 'max': 2 }},
 { 'name':'n_gram_offset',
 'type': 'int',
 'bounds': { 'min': 0, 'max': 2 }},
 { 'name':'log_min_df',
 'type': 'double',
 'bounds': { 'min': math.log(0.00000001), 'max': math.log(0.1) }},
 { 'name':'df_offset',
 'type': 'double',
 'bounds': { 'min': 0.01, 'max': 0.25 }}
 ],
}).experiment

# run experimentation loop
for _ in range(60):
 suggestion = conn.experiments(experiment.id).suggest().suggestion
 opt_metric = sentiment_metric(POSITIVE_TEXT, NEGATIVE_TEXT,
 suggestion.assignments)
 conn.experiments(experiment.id).report(data={
 'assignments': suggestion.assignments,
 'value': opt_metric,
 })
# track progress on your experiment : https://sigopt.com/experiment/list
```

Behold the power of abstraction! It's pretty amazing that we can build and tune a model in so few lines of code. It should be noted however that this experiment **takes about 40 min** to complete (and costs about $0.052 if you use a t2.medium) so letting it run in a background session on a remote machine allows you to safely start it and get back to working on other things. You can periodically check in on the progress using the SigOpt [experiment dashboard](https://sigopt.com/experiment/list).****

## Objective Metric : $f(λ)$

SigOpt finds parameter configurations that **maximize** any metric, so we need to pick one that is appropriate for this classification task. We'll use $ f(\lambda) $ to denote our objective metric function and \lambda to represent the set of tunable parameters, which we discuss in the following section. In designing our objective metric, [accuracy](https://en.wikipedia.org/wiki/Accuracy_and_precision), the number of correctly classified reviews, is obviously important, but we also want assurance that our model generalizes and can perform well on data on which it was not trained. This is where the idea of [cross-validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) comes into play.

Cross-validation requires us to split up our entire labeled dataset $ \mathcal{D} $ into two distinct sets: one to train on $ \mathcal{D}_{\text{train}} $ and one to validate our trained classifier on $ \mathcal{D}_{\text{valid}} $. We then consider metrics like accuracy on only the validation set. Taking this further and considering not one, but many possible splits of the labeled data is the idea of [k-fold cross-validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)#k-fold_cross-validation) where multiple training, validation sets are generated and validation metrics can be aggregated in several ways (e.g., mean, min, max) to give a single estimation of performance.

In this case, we'll use the mean of the k-folded cross-validation accuracies [2]. In our case, $ k = 5 $ folds are used and the train and validation sets are split randomly using 70% and 30% of the entire dataset, respectively.

$$ \mathcal{L}(\pmb{\lambda}, \mathcal{D}_{\text{t}}, \mathcal{D}_{\text{v}}) = \text{acc. of LR}(\pmb{\lambda}, \mathcal{D}_{\text{t}}) \text{ on } \mathcal{D}_{\text{v}} $$ $$ f(\pmb{\lambda} ) = \frac{1}{k} \sum_{i=1}^{k} \mathcal{L}(\pmb{\lambda}, \mathcal{D}^{(i)}_{\text{train}}, \mathcal{D}^{(i)}_{\text{valid}}) $$

This objective metric $ f(\lambda) $ takes on values in the range [0, 1.0], where 0 represents a mis-classification of every example in all validation folds and 1.0 represents perfect classification on all validation folds. The higher the cross-validation metric, the better our classifier is doing.

Using many folds might not be practical if **training takes an very long time** (you might have to settle for 1 or 2 folds only); indeed, **SigOpt is the perfect tool** for tuning these time consuming and expensive to evaluate functions!

## Tunable Parameters : $ λ $

The objective metric is controlled by a set of parameters that potentially influence its performance. [Parameters](https://sigopt.com/docs/objects/parameter) can be defined on integer, continuous, or categorical domains. The parameters used in this experiment can be split into two groups: those governing the feature representation of the review text (`min_n_gram`, `ngram_offset`, `log_min_df`, `df_offset`) and those governing the cost function of logistic regression (`log_reg_coef`, `l1_coef`). We explain these parameters in more detail below.

## Feature Representation Parameters

The [CountVectorizer](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.CountVectorizer.html) class in scikit-learn is a convenient mechanism for transforming a corpus of text documents into vectors using [bag of words representations (BOW)](https://en.wikipedia.org/wiki/Bag-of-words_model). scikit-learn offers quite a bit of control in determining which [n-grams](https://en.wikipedia.org/wiki/N-gram) make up the vocabulary for your BOW vectors. As a quick refresher, n-grams are sequences of text tokens as shown in the example below:

**Original Text** | "SigOpt optimizes any complicated system"
----------------- | --------------------------------------------------------------
**1-grams**       | { "SigOpt", "optimizes", "any", "complicated", "system"}
**2-grams**       | { "SigOpt_optimizes", "optimizes_any", "any_complicated" ... }
**3-grams**       | { "SigOpt_optimizes_any", "optimizes_any_complicated" ... }

The number of times each n-gram appears in a given piece of text is then encoded in the BOW vector describing that text. CountVectorizer allows you to control the range of n-grams that are included in the vocabulary (`min_n_gram`, `ngram_offset` in our experiment), as well as filtering n-grams outside a specified [document-frequency](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) range (`log_min_df`, `df_offset` in our experiment). For example, if a rare 3-gram like "hi_diddly_ho" doesn't appear with at least min-df frequency in the corpus, it is not included in the vocabulary. Similarly, n-grams that occur in nearly every document (1-grams like "the", "a" etc) can also be filtered using the max-df parameter. Often when the range of the parameter is very large or very small, it makes sense to look at the parameter on the [log scale](https://en.wikipedia.org/wiki/Logarithmic_scale), as we do with the `log_min_df` parameter.

Which n-gram vocabulary performs best on this classification problem? Including these vocabulary parameters in our experiment gives SigOpt the power to explore this important question automatically.

## Logistic Regression Error Cost Parameters

Using the [SGDClassifier](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.SGDClassifier.html) class in scikit-learn, we can succinctly formulate and solve the logistic regression learning problem. The error function for logistic regression, two-class classification is defined in the following way:

$$ E(\pmb{\theta}) = \frac{1}{M}\sum_{i=1}^{M}\log\left( 1.0 + e^{-y_i(\pmb{\theta}^T\mathbf{x_i}) }\right ) + \alpha\left( \frac{1-\rho}{2}\|\pmb{\theta}\|_2^{2} + \rho\|\pmb{\theta}\|_1\right) $$

Where

$M$ = number of training examples<br>
$\theta$ = vector of weights the algorithm will learn for each n-gram in vocabulary<br>
$y_{i}$ - training data label : {-1, 1} for our two class problem<br>
$x_{i}$ - training data input vector: BOW vectors described in previous section<br>
$\alpha$ - weight of regularization term (`log_reg_coef` in our experiment)<br>
$\rho$ - weight of l1 norm term (`l1_coef` in our experiment)

The first term of the cost function penalizes weights that do not fit the training data while the second term penalizes model complexity (how far are the feature weights away from zero). scikit-learn performs [stochastic gradient descent](https://en.wikipedia.org/wiki/Stochastic_gradient_descent) on this error function with respect to the weights in an attempt to find those that minimize this function. Check out another one of our blog posts where we discuss in more detail the [trade off between fidelity and regularization](http://blog.sigopt.com/post/132959177928/sigopt-fundamentals-approximation-of-data)

Should we use [l1 or l2 regularization](https://en.wikipedia.org/wiki/Regularization_(mathematics)), or perhaps a [weighted mixture](https://en.wikipedia.org/wiki/Elastic_net_regularization)? How much should the entire regularization term be weighted? With this error formulation, and the $ \alpha $ and $ \rho $ parameters exposed to our experiment, SigOpt can quickly find these answers to these important questions.

## Optimization Performance

SigOpt offers one solution to the [hyperparameter optimization](https://en.wikipedia.org/wiki/Hyperparameter_optimization) problem, however there are other existing techniques. In particular, [random search and grid search](http://scikit-learn.org/stable/modules/grid_search.html) are two commonly employed strategies. Random search, as you might guess, simply selects parameter configurations at random, while grid search sweeps through a selected subset of the parameter space.

How should we evaluate the performance of these alternative optimization strategies? One criterion that makes sense is to consider the **best found** (max) value of the objective metric. Better performing strategies will find better configurations over the duration of their search. Due to the stochastic nature of these systems however, we must consider the variation in our best found measurements over several runs to make fair comparisons.

To ground our discussion, we also report the performance when no hyperparameter optimization is performed, and we simply take the default values for CountVectorizer and SGDClassifier as provided by scikit-learn. For grid search, we consider 64 evenly spaced parameter configurations (order shuffled randomly) across our domain and analyze the best seen after 60 evaluations to be consistent with our limit on the total number of evaluations for this experiment. Exhaustive grid search is usually prohibitive because the number of possible configurations grows exponentially. For example if we considered 10 configurations for each parameter in a problem like ours with 6 parameters, we would have 1 million ( $ 10^{6} $ ) total joint configurations to evaluate.

Results averaged over 20 runs, each run consisting of 60 function evaluations

           | SigOpt                  | Rnd. Search     | Grid Search     | No Tuning (Baseline)
---------- | ----------------------- | --------------- | --------------- | --------------------
Best Found | **0.8760** **(+5.72%)** | 0.8673 (+4.67%) | 0.8680 (+4.76%) | 0.8286

These are promising results! **SigOpt finds the best configuration** with statistical significance over the other two approaches (p = 0.0001, using the unpaired [Mann-Whitney U test](https://en.wikipedia.org/wiki/Mann%E2%80%93Whitney_U_test)) and improves the performance as compared to the baseline by 5.72%.

## Closing Remarks

This short example scratches the surface of the types of ML related experiments one could conduct using SigOpt. For example, SGDClassifier has lots of variations from which to select– another experiment might be to treat the loss function as a [categorical variable](https://sigopt.com/docs/objects/parameter). What sort of models are you building that could benefit from better experimentation or optimization? Stay tuned for more posts in our series on integrating SigOpt with various ML frameworks to **solve real problems more efficiently!**

Get [the code](https://github.com/sigopt/sigopt-examples/tree/master/text_classifier) from this example and [sign up for SigOpt](https://sigopt.com/signup) for free to try out this experiment yourself!

## References

[1]: John Blitzer, Mark Dredze, Fernando Pereira. _Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification._ Association of Computational Linguistics (ACL), 2007\. [[PDF](http://www.cs.jhu.edu/~mdredze/publications/sentiment_acl07.pdf)]

[2]: Eggensperger, K., Feurer, M., Hutter F., et al. _Towards an Empirical Foundation for Assessing Bayesian Optimization of Hyperparameters._ NIPS workshop on Bayesian Optimization in Theory and Practice, 2013\. [[PDF](http://www.cs.ubc.ca/~hutter/papers/13-BayesOpt_EmpiricalFoundation.pdf)]
