# Unsupervised Learning with Even Less Supervision Using Bayesian Optimization

[Original URL](http://blog.sigopt.com/post/140871698423/sigopt-for-ml-unsupervised-learning-with-even)

> By: Ian Dewancker, Research EngineerIn this post on integrating SigOpt with machine learning frameworks, we will show you how to use SigOpt and XGBoost to efficiently optimize an unsupervised...

By: [Ian Dewancker, Research Engineer](http://t.umblr.com/redirect?z=https%3A%2F%2Fsigopt.com%2Fabout&t=Zjg0NWE4MDI5MTNiODlkZjUxNTU5NjM0YWIxMWYzODNkZDFiMjkzNyxCcGFRb3JySQ%3D%3D)****

In this post on integrating SigOpt with machine learning frameworks, we will show you how to use [SigOpt](http://t.umblr.com/redirect?z=https%3A%2F%2Fsigopt.com%2F&t=ODc2ZDczZDViYjJiMzYzZjFlNzc3OGQ1YjQ2MTkyMzhlNzVhZGRiZSxCcGFRb3JySQ%3D%3D) and [XGBoost](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fdmlc%2Fxgboost&t=YWQ2MjBlMWI5YzBhY2YwYTExMWJjNzIxMjY1YTgxZjFmNDg0ODhlNyxCcGFRb3JySQ%3D%3D) to efficiently optimize an [unsupervised learning](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FUnsupervised_learning&t=YTUzMzRiNThiOGYyMmY4MWZiMGNkMDE5NTkyYjExMGU1ODEzY2MzYSxCcGFRb3JySQ%3D%3D) algorithm's hyperparameters to increase performance on a classification task.

As we [previously](http://blog.sigopt.com/post/133089144983/sigopt-for-ml-automatically-tuning-text) discussed, fully supervised learning algorithms require each data point to have an associated class or output. In practice, however, it is often the case that relatively few labels are available during training time and **labels are costly or time consuming to acquire**. For example, it might be a very slow and expensive process for a group of experts to manually investigate and classify thousands of credit card transaction records as fraudulent or legitimate. A better strategy might be to study the large collection of transaction data without labels, building a representation that better captures the variations in the transaction data automatically.

## Unsupervised Learning

Unsupervised learning algorithms are designed with the hope of capturing some useful latent structure in data. These techniques can often enable dramatic gains in performance on subsequent supervised learning tasks, without requiring more labels from experts. In this post we will use an unsupervised method on an image recognition task posed by researchers at Stanford [1] where we try to recognize house numbers from images collected using Google street view ([SVHN](http://t.umblr.com/redirect?z=http%3A%2F%2Fufldl.stanford.edu%2Fhousenumbers%2F&t=ODZkMjI0MTk4NGRmYzcwMTNjYTNiNjJkNzRkY2ZjMjg3ZGZkOWIzNyxCcGFRb3JySQ%3D%3D)). This is a more challenging problem than [MNIST](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMNIST_database&t=YjllYmFhYzdiNmE2MjNkZDgyOWNiMjAwM2MzNjdjOTYxYzY1MmY0ZixCcGFRb3JySQ%3D%3D) (another popular digit recognition data set) as the appearance of each house number varies quite a bit and the images are often cluttered with neighboring digits:

![image](http://36.media.tumblr.com/897974b22474f1cec2418525e8a44106/tumblr_inline_o3w5crmZt31toi3ym_540.png) _Figure 1:_ 32x32 cropped samples from the classification task of the SVHN dataset. Each sample is assigned only a single digit label (0 to 9) corresponding to the center digit. (Sermanet [6])

In this example, we assume access to a large collection of unlabelled images $X_u$, where the correct answer is not known, and a relatively small amount of labelled data $(X_s, y)$ for which the true digit in each image is known (often requiring a non-trivial amount of time and money to collect). Our hope is to find a suitable unsupervised model, built using our large collection of unlabelled images, that transforms images into a more useful representation for our classification task.

Unsupervised and supervised learning algorithms are typically governed by small sets of hyperparameters $(\lambda_u, \lambda_s)$, that control algorithm behavior. In our example pipeline below, $X_u$ is used to build the unsupervised model $f_u$ which is then used to transform the labelled data $(X_s, y)$ before the supervised model $f_s$ is trained. Our task is to efficiently search for good hyperparameter configurations $(\lambda_u, \lambda_s)$ for both the unsupervised and supervised algorithms. SigOpt minimizes the classification error $E(\lambda_u, \lambda_s)$ by sequentially generating suggestions for the hyperparameters of the model $(\lambda_u, \lambda_s)$. For each suggested hyperparameter configuration a new unsupervised data representation is formed and fed into the supervised model. The observed classification error is reported and the process repeats, converging on the set of hyperparameters that minimizes the classification error.

![image](http://41.media.tumblr.com/fb2c1b875979f4eaa672c021e13c6c97/tumblr_inline_o3w5goCmkq1toi3ym_500.png) Figure 2 : Process for coupled unsupervised and supervised model tuning

Data scientists use their domain knowledge to select appropriate unsupervised and supervised models, but the task of selecting the best parameters for these models is often daunting and non-intuitive. Even simple unsupervised models--such as the whitening strategy we discuss below--often introduce tunable parameters, leaving potentially good models on the table simply because a winning configuration was never found.

[SigOpt](http://t.umblr.com/redirect?z=https%3A%2F%2Fsigopt.com%2F&t=ODc2ZDczZDViYjJiMzYzZjFlNzc3OGQ1YjQ2MTkyMzhlNzVhZGRiZSxCcGFRb3JySQ%3D%3D) offers Bayesian optimization as a service, capable of efficiently searching through the joint variations $(\lambda_u, \lambda_s)$ of both the supervised and unsupervised aspects of machine learning systems (Figure 2.) This allows experts to unlock the power of unsupervised strategies with the assurance that each model is reaching its full potential automatically. The gains achieved by methods like SigOpt are additive with feature engineering, allowing for better results and faster iteration with less trial and error.

## Show Me The Code

The source code for this experiment and setup script are [available here](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fsigopt%2Fsigopt-examples%2Ftree%2Fmaster%2Funsupervised-model&t=ZjM0MDE5ZTM0MWNlMTc0MDVhMTFmZmYxOTYwOGE5NzY5MDRlNDdjZixCcGFRb3JySQ%3D%3D). SigOpt can quickly identify the optimal configuration of these complicated models, [much faster than traditional methods](http://blog.sigopt.com/post/111903668663/tuning-machine-learning-models) such as grid search and random search, especially when more than 2 or 3 hyperparameters are at play. Finding optima more quickly can also drastically save on computational resource costs (e.g. AWS instances) while still achieving comparable or better model performance.

## Unsupervised Model

We start with the initial features describing the data: raw pixel intensities for each image. The goal of the unsupervised model is to transform the data from its original representation to a new (more useful) learned representation without using labeled data. Specifically, you can think of this unsupervised model as a function $f : \mathbb{R}^N \rightarrow \mathbb{R}^J$. Where $N$ is the number of features in our original representation and $J$ is the number of features in the learned representation. In practice, expanded representations (sometimes referred to as a feature map) where $J$ is much larger than $N$ often work well for improving performance on classification tasks [2].

### Image Transform Parameters ($s$, $w$, $K$)

A simple but surprisingly effective transformation for small images was proposed in a paper by Coates [1] where image patches are transformed into distances to $K$ learned centroids (average patches) using the [k-means algorithm](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FK-means_clustering&t=N2E2ODZkMDBjOTMyNWRiOWYwMTdkZDdjYmYzZjg2OWY4NDEyM2U0MixCcGFRb3JySQ%3D%3D), and then pooled together to form a final feature representation as outlined in the figure below:

![image](http://41.media.tumblr.com/b3bc1afc6fa40549143e147d6e536fd9/tumblr_inline_o3w5fc12Wy1toi3ym_500.png) _Figure 3:_ Feature extraction using a w-by-w receptive field and stride s. w-by-w patches separated by s pixels each, then map them to K-dimensional feature vectors to form a new image representation. These vectors are then pooled over 4 quadrants of the image to form classifier feature vector. ( Coates [1] )

In this example we are working with the 32x32 (n=32) converted gray-scale (d=1) images of the SVHN dataset. We allow SigOpt to vary the stride length ($s$) and patch width ($w$) parameters. The figure above illustrates a pooling strategy that considers quadrants in the 2x2 grid of the transformed image representation, summing them to get the final transformed vector. We used the suggested resolution in [1] and kept $\text{pool_res}$ fixed at 2\. $f(x)$ represents a $K$ dimensional vector that encodes the distances to the $K$ learned centroids, and $f_i(x)$ refers to the distance of instance $x$ to centroid $i$. In this experiment, $K$ is also a tunable parameter. The final feature representation of each image will have $J = K automatically_tuning_text_classifiers.epub automatically_tuning_text_classifiers.md data.json data_ml devops docker embedded gaming github go how_to_write_scripts_using_awk_programming_language_–_part_13.epub how_to_write_scripts_using_awk_programming_language_–_part_13.md html javascript linux mobile png programming science social tidy url_to_filename.csv using_model_tuning_to_beat_vegas.epub using_model_tuning_to_beat_vegas.md web_dev \text{pool_res}^2$ features.

### Whitening Transform Parameter ($\epsilon_{\text{zca}}$)

Before generating the image patch centroids and any subsequent patch comparisons to these centroids, we apply a [whitening](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FWhitening_transformation&t=ZTZmYjIyYTE2MzczYzQ0NmNkMTVhMWY1NWE0YjNjZjkyOGJjMTJiMyxCcGFRb3JySQ%3D%3D) transform to each patch. When dealing with image data, whitening is a common preprocessing transform which removes the correlation between all pairs of individual pixels [3]. Intuitively, it can be thought of as a transformation that highlights contrast in images. It has been shown to be helpful in image recognition tasks, and may also be useful for other feature data. The figure below shows several example image patches before and after the whitening transform is applied.

![image](http://40.media.tumblr.com/48853be2878b7f08e99583215694670e/tumblr_inline_o3w5j3zli71toi3ym_500.png) _Figure 4:_ Comparison of image patches before and after whitening ( Stansbury [7] )

The whitening transformation we use is known as ZCA whitening [4]. This transform is achieved by cleverly applying the eigendecomposition of the covariance matrix estimate to a mean adjusted version of the data matrix, so that the expected covariance of the data matrix becomes the identity. A regularization term $\epsilon_{\text{zca}}$ is added to the diagonal eigenvalue matrix, and $\epsilon_{\text{zca}}$ is exposed as a tunable parameter to SigOpt.

![image](http://41.media.tumblr.com/f86ff26845cea3cf57d7984805193f0c/tumblr_inline_o3w62kRyEy1toi3ym_540.png)

```
# fit cov estimate to data matrix X (n x m, n samples, m feats)
cov = LedoitWolf().fit(X)
D, U = numpy.linalg.eigh(cov.covariance_)
V = numpy.sqrt(numpy.linalg.inv(numpy.diag(D+eps_zca)))
Wh = numpy.dot(numpy.dot(U,V),U.T) # ZCA whitening transform matrix
mu = numpy.mean(X, axis=0)
X_zca = numpy.dot(X-mu, Wh)
```

### Centroid Distance Sparsity Parameter ($\text{sparse_p}$)

Each whitened patch in the image is transformed by considering the distances to the learned $K$ centroids. To control this sparsity of the representation we report only distances that are below a certain percentile, $\text{sparse_p}$, when considering the pairwise distances between the current patch and the centroids. Intuitively this acts as a threshold which allows for only the "close" centroids to be active in our representation.

```
# compute distances between patches and all centroids
Z = k_means.transform(img_ptchs)
tau = numpy.percentile(Z, sparse_p, axis=1, keepdims=True)
Z = numpy.maximum(0, tau - Z)
```

The figure below illustrates the idea with a simplified example. A whitened image patch (in the upper right) is compared against the 4 learned centroids after k-means clustering. Here, let's imagine we have set the percentile threshold to 50, so only the distances in the lower half of all centroid distances persist in the final representation, the others are zeroed out

![image](http://40.media.tumblr.com/76c61d2b0435baf6ed96ac9e49abd8a4/tumblr_inline_o3w5pjeNEL1toi3ym_500.png) _Figure 5:_ Sparsity transform; distances from a patch to centroids above 50th percentile are set to 0

While the convolutional aspects of this unsupervised model are tailored to image data, the general approach of transforming feature data into a representation that reflects distances to learned archetypes seems suitable for other data sets and feature spaces [8].

## Supervised Model

With the learned representation of our data, we now seek to maximize performance on our classification task using a smaller labelled dataset. While [random forests](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FRandom_forest&t=NTIwZjI4MGM1ZTkxNjdiODVlNWIyYjRkNTk4ZTY3N2VmZmI2NDI2ZixCcGFRb3JySQ%3D%3D) are an excellent, and simple, classification tool, better performance can typically be achieved by using carefully tuned ensembles of [boosted](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FBoosting_%28machine_learning%29&t=NDJmOGQ0OTMzMTg1YTEyNjRhYmMwZTM5NGY4OGIzMDcyMjU5OGUyMCxCcGFRb3JySQ%3D%3D) classification trees.

### Gradient Boosting Parameters ($\gamma, \theta, M$)

We consider the popular library [XGBoost](http://t.umblr.com/redirect?z=https%3A%2F%2Fgithub.com%2Fdmlc%2Fxgboost&t=YWQ2MjBlMWI5YzBhY2YwYTExMWJjNzIxMjY1YTgxZjFmNDg0ODhlNyxCcGFRb3JySQ%3D%3D) as our gradient boosting implementation. Gradient boosting is a generic boosting algorithm that incrementally builds an [additive model](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FAdditive_model&t=ODExM2NiN2QxYTk2YjliZDFjMzU4ODQ1NThlMmNkMjM2ZTQwMzVjMSxCcGFRb3JySQ%3D%3D) of base learners, which are themselves simpler classification or regression models. Gradient boosting works by building a new model at each iteration that best reconstructs the gradient of the loss function with respect to the previous ensemble model. In this way it can be seen as a sort of functional gradient descent, and is outlined in more detail below. In the pseudocode below we outline building an ensemble of [regression trees](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FDecision_tree_learning&t=MzVhNmEwMDM0MTgzZjQwMTQ2ZWVmNzM0ZTBiZDAzMGVjNWM0NTMwNixCcGFRb3JySQ%3D%3D), but the same method can be used with a classification loss function $L$

![image](http://41.media.tumblr.com/007050d2b8785221a5a4af1a3ceb6126/tumblr_inline_o3w60xQOKL1toi3ym_500.png) _Algorithm 1:_ Pseudocode for supervised gradient boosting using regression trees as base learners

Important parameters governing the gradient boosting algorithm include $M$, the number of base learner models in the ensemble, and $\gamma$ the learning rate, which controls the relative contribution of each new base learner in the final additive model. Each base learner is also governed by it's own set of parameters $\theta$. Here we consider classification trees as our base learners, governed by a familiar set of parameters managing tree growth and regularization (e.g., max depth, sub_sample). We expose these parameters to SigOpt to optimize simultaneously with the parameters governing the unsupervised transformation discussed previously.

## Classification Performance

To compare model performance, we use accuracy, which can be understood as a measure of the probability of correctly classifying for each image in the test set. For example, a model that correctly recognizes the house numbers for 91% of the images would result in an accuracy score of 0.91.

We compare the ability of SigOpt to find the best hyperparameter configuration to the industry standard methods of random search, which usually outperforms grid search and manual search (Bergstra [9]) and a baseline of using an untuned model.

Because the underlying methods used are inherently stochastic we performed 10 independent hyperparameter optimizations using both SigOpt and random search for both the purely supervised and combined models. Hyperparameter optimization was performed on the accuracy estimate from a 80/20 cross validation fold of the training data (73k examples). The 'extra' set associated with the SVHN dataset (530K examples) was used to simulate the unlabelled data $X_u$ in the unsupervised parts of this example.

For the unsupervised model 90 sequential configuration evaluations (~50 CPU hrs) were used for both SigOpt and random search. For the purely supervised model 40 sequential configuration evaluations (~8 CPU hrs) were used for both SigOpt and random search. In practice, SigOpt is usually able to find good hyperparameter configurations with a number of evaluations equal to 10 times the number of parameters being tuned (9 for the combined model, 4 for the purely supervised model). The same parameters and domains were used for XGBoost in both the unsupervised and purely supervised settings. As a baseline, the hold out accuracy of an untuned scikit-learn random forest using the raw pixel intensity features.

After hyperparameter optimization was completed for each method we compared accuracy using a completely held out data set (SHVN test set, 26k examples) using the best configuration found in the tuning phase. The hold out dataset was run 10 times for each best hyperparameter configuration for each method, the mean of these runs is reported in the table below. SigOpt outperforms random search with a p-value of 0.0008 using the unpaired [Mann-Whitney U test](http://t.umblr.com/redirect?z=https%3A%2F%2Fen.wikipedia.org%2Fwiki%2FMann%25E2%2580%2593Whitney_U_test&t=YjNjMTlkMjAxNTRhMjhkNTY4MjJhMTdjMTc2MzVhOTgzMDgwZjRiZSxCcGFRb3JySQ%3D%3D).

             | SigOpt<br>
(xgboost +<br>
Unsup. Feats) | Rnd Search<br>
(xgboost +<br>
Unsup. Feats) | SigOpt<br>
(xgboost +<br>
Raw Feats) | Rnd Search<br>
(xgboost +<br>
Raw Feats) | No Tuning<br>
(sklearn RF +<br>
Raw Feats)
------------ | --------------------------------------- | ------------------------------------------- | ------------------------------------ | ---------------------------------------- | ------------------------------------------
Hold out ACC | **0.8601** **(+49.2%)**                 | 0.8190                                      | 0.7483                               | 0.7386                                   | 0.5756

_Table 1:_ Comparison of model accuracy on held out (test) dataset after different tuning strategies

The chart below tracks the optimization path of SigOpt vs random search optimization strategies when tuning the unsupervised model (Unsup Feats) and only the supervised model (Raw Feats). We plot the interquartile range of the best seen cross validated accuracy score on the training set at each objective evaluation during the optimization. As mentioned above, 90 evaluations were used in the optimization of the unsupervised model and 40 in the supervised setting. SigOpt outperforms random search in both settings on this training data (p-value 0.005 using the same Mann-Whitney U test as before).

![image](http://40.media.tumblr.com/2f8df69eb0d69e4f677e39e6a9c1a87b/tumblr_inline_o3w5klDLmF1toi3ym_500.png) _Figure 6:_ Optimization traces of CV accuracy using SigOpt and random search

## Closing Remarks

Unsupervised learning algorithms can be a powerful tool for boosting the performance of your supervised models when labelling is an expensive or slow process. Tuning automatically brings each model to its full potential. [SigOpt](http://t.umblr.com/redirect?z=https%3A%2F%2Fsigopt.com%2F&t=ODc2ZDczZDViYjJiMzYzZjFlNzc3OGQ1YjQ2MTkyMzhlNzVhZGRiZSxCcGFRb3JySQ%3D%3D) was built to help with this non-intuitive task. As this example demonstrates, careful parameter tuning can enable engineering and data science teams to better leverage their unlabelled data and build more predictive data products in less time and lower cost. [Sign up](http://t.umblr.com/redirect?z=https%3A%2F%2Fsigopt.com%2Fsignup&t=YzZlY2FjNTU2YTU0YWUwMTkzNTkwNjJhZjI4OTU2NjhmMGQwNTM1OCxCcGFRb3JySQ%3D%3D) for a free evaluation today and get the most from your models!

## Additional Reading

SigOpt effectively optimizes machine learning models across a variety of datasets and algorithms. See our other examples:

- [Using model tuning to beat Vegas](http://blog.sigopt.com/post/136340340198/sigopt-for-ml-using-model-tuning-to-beat-vegas)
- [Automatically Tuning Text Classifiers](http://blog.sigopt.com/post/133089144983/sigopt-for-ml-automatically-tuning-text)

## References

[1]: Adam Coates, Honglak Lee, Andrew Y. Ng. An Analysis of Single-Layer Networks in Unsupervised Feature Learning. International conference on artificial intelligence and statistics (AISTATS). 2011\. [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Fai.stanford.edu%2F%7Eang%2Fpapers%2Fnipsdlufl10-AnalysisSingleLayerUnsupervisedFeatureLearning.pdf&t=MzFiOGIxNTY1NzhmYTk3ZmQxZWIxNDNmZDI3ODg2YmYxZjRkNTcwYyxCcGFRb3JySQ%3D%3D)]

[2]: Yoshua Bengio. Deep Learning of Representations for Unsupervised and Transfer Learning. JMLR Workshop Proceedings: Unsupervised and Transfer Learning. 2012 [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Fwww.iro.umontreal.ca%2F%7Elisa%2Fpointeurs%2FDL_tutorial.pdf&t=N2IwNDRlZTMyM2VmNDdkNWQyMDBiN2U0ZjIzMjgxM2QwOGIyZmZhYixCcGFRb3JySQ%3D%3D)]

[3]: Alex Krizhevsky, Geoffrey Hinton. Learning multiple layers of features from tiny images. 2009 [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Fwww.cs.toronto.edu%2F%7Ekriz%2Flearning-features-2009-TR.pdf&t=MDFmODcxZjBjZGMwMDIxYjE0NjA5MGJmNmUzN2JkOWNjMDZlOGJlZixCcGFRb3JySQ%3D%3D)]

[4]: Adam Coates, Andrew Y. Ng. Learning Feature Representations with K-means. 2012 [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Fwww.cs.stanford.edu%2F%7Eacoates%2Fpapers%2Fcoatesng_nntot2012.pdf&t=ZWZjMDQ3ZjgwM2JiODE5NGY4MmQ1NGNlOTEzNDkyNzYxNjIyMWU4ZSxCcGFRb3JySQ%3D%3D)]

[5]: Yuval Netzer, Tao Wang, Adam Coates, Alessandro Bissacco, Bo Wu, Andrew Y. Ng Reading Digits in Natural Images with Unsupervised Feature Learning. NIPS Workshop on Deep Learning and Unsupervised Feature Learning. 2011 [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Fufldl.stanford.edu%2Fhousenumbers%2Fnips2011_housenumbers.pdf&t=YmFiYmU4YzA0YzBmNDgxMGQ1MmNiMzA3YmI1NGRjYWY5YTQ3OTU1OCxCcGFRb3JySQ%3D%3D)]

[6]: Pierre Sermanet, Soumith Chintala and Yann LeCun. Convolutional Neural Networks Applied to House Numbers Digit Classification. Pattern Recognition International Conference (ICPR). 2012\. [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Farxiv.org%2Fpdf%2F1204.3968.pdf&t=YTFkZDIzNzI5N2UxY2IwNDNjODU5YmQxYTQ2MDBkZThlOGVhYTk0OSxCcGFRb3JySQ%3D%3D)]

[7]: Dustin Stansbury. The Statistical Whitening Transform. The Clever Machine. 2014\. [[LINK](http://t.umblr.com/redirect?z=https%3A%2F%2Ftheclevermachine.wordpress.com%2F2013%2F03%2F30%2Fthe-statistical-whitening-transform%2F&t=OWQzNzk3NTA2NWQwNzc1YmI1ODM1ZmIzZTM2OTVkZTAxZDQ1NGI5ZixCcGFRb3JySQ%3D%3D)]

[8]: Sander Dieleman and Benjamin Schrauwen. Multiscale Approaches to Music and Audio Feature Learning. International Society for Music Information Retrieval Conference. 2013\. [[PDF](http://t.umblr.com/redirect?z=https%3A%2F%2Fbiblio.ugent.be%2Fpublication%2F4152117%2Ffile%2F4324540.pdf&t=YjY5MjEyZmEwMzQ4ZTRmMzUzYzYzNGM1NDFkZDI1ZmQ4NzIwNWFiZixCcGFRb3JySQ%3D%3D)]

[9]: James Bergstra and Yoshua Bengio. Random search for hyper-parameter optimization. The Journal of Machine Learning Research (JMLR). 2012 [[PDF](http://t.umblr.com/redirect?z=http%3A%2F%2Fwww.jmlr.org%2Fpapers%2Fvolume13%2Fbergstra12a%2Fbergstra12a&t=NzQxMGRiYzZjYjE5ZTI4MTFiZmZmMzQ3MDg3ZjA0YzIxMjczYjg3NixCcGFRb3JySQ%3D%3D)]
