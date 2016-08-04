# Machine Learning: Bayes

[Original URL](http://blog.rokkincat.com/machine-learning-bayes/)

> Welcome to machine learning week 2\. If you did not check out the introduction yet, go check it out. It is about cool scatterplot stuff – don't worry, this post will still be here 😊. Once...

Welcome to machine learning week 2\. If you did not check out the [introduction](http://blog.rokkincat.com/machine-learning-introduction-scatterplots) yet, go check it out. It is about cool scatterplot stuff – don't worry, this post will still be here 😊. Once again, this content is a supplement to the [Udacity course](https://www.udacity.com/course/viewer#!/c-ud120/l-2410328539) on machine learning, so go check that out as well!

So now that we have a handle on graphically representing data, let's get started with our first analysis technique. The technique is called Naive Bayes, and it is used heavily in natural language and text processing. With this strategy we can analyze the frequency of words to solve problems.

## Sensitivity vs Specificity

Bayes rule revolves around two main terms: sensitivity and specificity. Sensitivity measures the amount of true positives in a population, while specificity measures the amount of true negatives in a population.

Imagine going to the doctor and getting tested for strep throat. If the test comes back positive, and you really have strep throat, then you would be placed in the _true positive group_. Therefore, you would _contribute to a rise in the sensitivity_.

Likewise, if you went to the doctor and the test results were negative, (and do not have strep throat in actuality), then you would be placed in the _true negative group_. This also means that you would _contribute to a rise in the specificity_.

![Sensitivity vs Specificity](http://blog.rokkincat.com/content/images/2016/02/spec.png)

In the image above, we have Positive Predictive Value (PPV) and Negative Predictive Value (NPV).

_PPV = TruePositive / (TruePositive + FalsePositive)_

_NPV = TrueNegative / (TrueNegative + FalseNegative)_

Depending on the situation, it may be _more_ beneficial to focus on maximizing the accuracy of one over the other; however this is not always the case. For a moment, consider optimizing cancer tests. What would be the best strategy?

Would it be good to maximize sensitivity in cancer tests (leaving specificity to suffer)? Probably not. If patients are diagnosed with cancer (without actually having it), then this would mean that patients without cancer would be receiving chemotherapy treatments. On the same note, it wouldn't be a good idea to maximize specificity because this would mean that people with cancer have a greater chance of not being diagnosed.

## Naive Bayes

Naive Bayes is an approach for classifying data based on frequency. Suppose you enjoy reading about the latest technology, and you subscribe to the Apple and Microsoft blogs. You would probably expect "ipad", "mobile", and "iphone" in an Apple post, while Microsoft would have "windows", and "surface". So if a trained classifier was given "Macs are awesome", it would most likely return "Apple".

A simple coding example to demonstrate the power of Naive Bayes uses the sklearn library. Here we will see how the computer associates "Coffee is the best".

The necessary classes for doing Naive Bayes are CountVectorizer and GaussianNB. If you browse the documentation a little bit, you may notice that there are much more complicated algorithms that could yield better results. The usage for these other algorithms are basically the same.

```
import numpy as np 
from sklearn.feature_extraction.text import CountVectorizer 
from sklearn.naive_bayes import GaussianNB 
```

Now we will have some phrases about coffee and soda. If we were doing blog posts, each index in the features array would contain a blog post. We will also make a phrase that the computer will be tasked to classify.

```
features = [ 
 "Coffee is healthy.",
 "Coffee is caffeinated.",
 "Coffee is made from beans.",
 "Coffee is made with hot water.",
 "Little kids in Mexico drink coffee.",
 "Soda is loaded with sugar.",
 "Soda is unhealthy.",
 "Soda is manufactured by large corporations.",
 "Soda can be packaged in a can or bottle.",
 "Soda is caffeinated."
]
predictable = ["Coffee is the best"] 
```

Now is the time for the fun part. We will employ our classes to convert the words to vectors, and then we will train the classifier with the features and labels. Finally we will attempt to classify the `predictable` phrase.

```
cv = CountVectorizer(input="content") 
features = cv.fit_transform(features).toarray()

predictable = np.array(cv.transform(predictable).toarray()) 
clf = GaussianNB() 
clf.fit(features, [0,0,0,0,0,1,1,1,1,1])

prediction = clf.predict(predictable) 
print prediction 
>> [0]
```

And as you would expect, we receive the proper classification.

### All of the Code

```
import numpy as np 
from sklearn.feature_extraction.text import CountVectorizer 
from sklearn.naive_bayes import GaussianNB

features = [ 
 "Coffee is healthy.",
 "Coffee is caffinated.",
 "Coffee is made from beans.",
 "Coffee is made with hot water.",
 "Little kids in Mexico drink coffee.",
 "Soda is loaded with sugar.",
 "Soda is unhealthy.",
 "Soda is manufactured by large corporations.",
 "Soda can be packaged in a can or bottle.",
 "Soda is caffinated."
]

predictable = ["Coffee is the best"]

cv = CountVectorizer(input="content") 
features = cv.fit_transform(features).toarray()

predictable_feature = np.array(cv.transform(predictable).toarray()) 
clf = GaussianNB() 
clf.fit(features, [0,0,0,0,0,1,1,1,1,1])

prediction = clf.predict(predictable_feature) 
print prediction 
>> [0]
```

## Conclusion

The Naive Bayes approach is a useful algorithm to have in your toolbox. Remember, Naive Bayes is called "naive" because it does not consider word order when processing data – allowing us to achieve a quicker performance time. One drawback for this algorithm is that it does not consider sentence context very well.

Thank you for checking out this post, and be sure to tune in next week! ♡

## Behind the Scenes

In addition to talking about machine learning strategies, I will also have a section on lessons learned so that people interested in learning on their own will have an easier time getting setup.

### I was a little too confident

When I started this blog post, I wanted to develop a much more complicated example to be more impressive. When I realized that I've only worked with the library for about a week, I knew that I needed to simplify some things.

My original coding example was going to be training on a variety of different news sources and predicting the news source for a random article. My main troubles stemmed from not understanding the data structures for the parameters of the functions. Some of the functions required lists inside of lists, while others did not. There also seemed to be a higher level of complexity in the [tfidf vectorizer](http://scikit-learn.org/stable/modules/generated/sklearn.feature_extraction.text.TfidfVectorizer.html) which was a more complicated approach, but should have yielded better results.

All of these issues resulted in tons of generic exceptions and left me clueless as to what I was doing wrong. Even worse, since I was working with such large datasets, printing variables to the console did not help very much. It was very difficult to see what was going on when the console overflows with tons of data. In the future (_when I do work with large datasets_), I will start with a generic easy example with dummy data, and then gradually integrate the more complicated data.

So yeah, if you decide to do this, _please start with an easy example – you will save yourself from a lot of frustration_.

### Up and Running

Getting the environment setup was quite simple actually. Python is nice because all dependencies are global, so the only thing to do is install `numpy` and `sklearn`. This can easily be accomplished looking at the [documentation](http://scikit-learn.org/stable/install.html):

```
Scikit-learn requires: 
Python (>= 2.6 or >= 3.3), 
NumPy (>= 1.6.1), 
SciPy (>= 0.9). 
If you already have a working installation of numpy and scipy, the easiest way to install scikit-learn is using pip 

pip install -U scikit-learn 
```

Then just simply use the import statements that were shown in the code samples above.

### Finally

The additional research that I have put into building these blog posts seem to be teaching me more than the Udacity course itself; however Udacity does a good job providing a brief overview on the topics. This helped me gather a bit of understanding before conducting independent studies.
