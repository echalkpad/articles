# Three things I wish I knew earlier about Machine Learning

[Original URL](https://peadarcoyle.wordpress.com/2015/12/23/three-things-i-wish-i-knew-earlier-about-machine-learning/)

> I've been working with Machine Learning models both in academic and industrial settings for a few years now. I've recently been watching the excellent Scalable ML from Mikio Braun,...

I've been working with Machine Learning models both in academic and industrial settings for a few years now. I've recently been watching the excellent [Scalable ML](https://www.safaribooksonline.com/library/view/scalable-machine-learning/9781491939437/part09.html) from Mikio Braun, this is to learn some more about Scala and Spark.

His video series talks about the practicalities of 'big data' and so made me think what I wish I knew earlier about Machine Learning

1. **Getting models into production is a lot more than just micro services** 
2. ****Feature selection and feature extraction are really hard to learn from a book_**_
3. **The evaluation phase is really important**

I'll take each in turn.

**Getting models into production is a lot more than just micro services**

I gave a talk on [Data-Products](http://www.pyvideo.org/video/3590/how-to-get-data-science-models-into-production-on) and getting Ordinary Differential Equations into production. One thing that I didn't realise until sometime afterwards was just how challenging it is to handle issues like model decay, evaluation of models in production, dev-ops etc all by yourself. When I first used [ScienceOps](http://www.yhathq.com)from Yhat I underestimated how awesome it is. I struggle to find a direct competitor on the market right now, and I really think they've nailed a fascinating problem. Increasingly I realise I'm just not smart enough to deal with ops stuff – so I'm glad if I can outsource 

<span class="wp-smiley wp-emoji wp-emoji-smile" title=":)">:)</span>

**Feature selection and feature extraction are really hard to learn**

Something that I couldn't learn from a book, but tried to is feature selection and extraction. These skills are only learned by Kaggle competitions and real world projects. And learning about the various tricks and methods for this is something one learns only by implementing them or using them in real-world projects. This eats up a lot of the work flow of the data science process. In the new year I'll probably try to write out a blog post only on feature extraction and feature selection.

**The evaluation phase is really important**

Unless you apply your models to test data – you're not doing predictive analytics. Evaluation techniques such as cross-validation, evaluation metrics, etc are all invaluable as is simply splitting your data into test data and training data. Life often doesn't hand you a dataset with these things defined, so there is a lot of creativity and empathy involved in defining these two sets on a real world dataset. There is a great set of posts on [Dato](http://blog.dato.com/how-to-evaluate-machine-learning-models-the-pitfalls-of-ab-testing) about the challenges of model evaluation.

I think the explanations by [Mikio Braun](http://blog.mikiobraun.de/2015/03/three-things-about-data-science.html) are worth a read. I love his diagrams too and include it here in case you're not familiar with training sets and testing sets.

![3t-evaluation.png](https://peadarcoyle.files.wordpress.com/2015/12/3t-evaluation.png?w=584)

Source: Mikio Braun 2015

Often we don't discuss evaluation of models in papers, conferences or even when we talk about what techniques we use to solve problems. 'We used SVM on that' doesn't really tell me anything, it doesn't tell me your data sources, your feature selection, your evaluation methods, how you got into production, and how you used cross-validation or model-debugging. I think we need a lot more commentary about these 'dirty' aspects of machine learning. And I wish I knew that a lot earlier 

<span class="wp-smiley wp-emoji wp-emoji-smile" title=":)">:)</span>

My friend Ian has some great remarks on ['Data Science Delivered'](https://github.com/ianozsvald/data_science_delivered) which is a great read for any professional (junior or senior) who build machine learning models for a living. It is also a great read for recruiters hiring data scientists or managers interacting with data science teams – if you're looking for questions to ask people about – 'how did you handle that dirty data'.

I wish all my friends, readers and colleagues (past, present and future) a wonderful holiday season.

## Like this:

<span class="button">
  <span>Like</span>
</span>

 

<span class="loading">Loading…</span>

[]()
