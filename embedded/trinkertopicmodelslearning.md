# trinker/topicmodels_learning

[Original URL](https://github.com/trinker/topicmodels_learning)

> Topic Models Learning and R Resources This is a collection documenting the resources I find related to topic models with an R flavored focus. A topic model is a type of generative model used to...

## [](https://github.com/trinker/topicmodels_learning#topic-models-learning-and-r-resources)Topic Models Learning and R Resources

This is a collection documenting the resources I find related to topic models with an R flavored focus. A _topic model_ is a type of [_generative_](http://stackoverflow.com/questions/879432/what-is-the-difference-between-a-generative-and-discriminative-algorithm) model used to "discover" latent topics that compose a _corpus_ or collection of documents. Typically topic modeling is used on a collection of text documents but can be used for other modes including use as caption generation for images.

[![](https://github.com/trinker/topicmodels_learning/raw/master/inst/figure/topic-model.jpg)](https://github.com/trinker/topicmodels_learning/blob/master/inst/figure/topic-model.jpg)

## [](https://github.com/trinker/topicmodels_learning#table-of-contents)Table of Contents

## [](https://github.com/trinker/topicmodels_learning#just-the-essentials)Just the Essentials

This is my run down of the minimal readings, websites, videos, & scripts the reader needs to become familiar with topic modeling. The list is in an order I believe will be of greatest use and contains a nice mix of introduction, theory, application, and interpretation. As you want to learn more about topic modeling, the other sections will become more useful.

1. Boyd-Graber, J. (2013). [Computational Linguistics I: Topic Modeling](https://www.youtube.com/watch?v=4p9MSJy761Y)
2. Underwood, T. (2012). [Topic Modeling Made Just Simple Enough](http://tedunderwood.com/2012/04/07/topic-modeling-made-just-simple-enough/)
3. Weingart, S. (2012). [Topic Modeling for Humanists: A Guided Tour](http://www.scottbot.net/HIAL/?p=19113)
4. Blei, D. M. (2012). [Probabilistic topic models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Blei2012.pdf). _Communications of the ACM, (55)_4, 77-84\. doi:10.1145/2133806.2133826
5. inkhorn82 (2014). [A Delicious Analysis! (aka topic modelling using recipes)](http://rforwork.info/2014/02/17/a-delicious-analysis/) [(CODE)](https://gist.githubusercontent.com/inkhorn/9044779/raw/c7f0ba30d424aaeb75c5e221d12566f6732c4f29/recipe%20analysis.R)
6. Grüen, B. & Hornik, K. (2011). [topicmodels: An R Package for Fitting Topic Models.](https://github.com/trinker/topicmodels_learning/blob/master/articles/Gruen2011.pdf). _Journal of Statistical Software, 40_(13), 1-30.
7. Marwick, B. (2014a). [The input parameters for using latent Dirichlet allocation](http://stats.stackexchange.com/a/25128/7482)
8. Tang, J., Meng, Z., Nguyen, X. , Mei, Q. , & Zhang, M. (2014). [Understanding the limiting factors of topic modeling via posterior contraction analysis](https://github.com/trinker/topicmodels_learning/blob/master/articles/Tang2014.pdf). In _31 st International Conference on Machine Learning_, 190-198.
9. Sievert, C. (2014). [LDAvis: A method for visualizing and interpreting topic models](https://www.youtube.com/watch?v=IksL96ls4o0)
10. Rhody, L. M. (2012). [Some Assembly Required: Understanding and Interpreting Topics in LDA Models of Figurative Language](http://www.lisarhody.com/some-assembly-required)
11. Rinker, T.W. (2015). [R Script: Example Topic Model Analysis](https://raw.githubusercontent.com/trinker/topicmodels_learning/master/scripts/Example_topic_model_analysis.R)

## [](https://github.com/trinker/topicmodels_learning#key-players)Key Players

Papadimitriou, Raghavan, Tamaki & Vempala, Santosh (1997) first introduced the notion of topic modeling in their ["Latent Semantic Indexing: A probabilistic analysis"](https://github.com/trinker/topicmodels_learning/blob/master/articles/Papadimitriou1997.pdf). Thomas Hofmann (1999) developed "Probabilistic latent semantic indexing". Blei, Ng, & Jordan (2003) proposed _latent Dirichlet allocation_ (LDA) as a means of modeling documents with multiple topics but assumes the topic are uncorrelated. Blei & Lafferty (2007) proposed _correlated topics model_ (CTM), extending LDA to allow for correlations between topics. Roberts, Stewart, Tingley, & Airoldi (2013) propose a [_Structural Topic Model_](https://github.com/trinker/topicmodels_learning/blob/master/articles/Roberts2013.pdf) (STM), allowing the inclusion of meta-data in the modeling process.

## [](https://github.com/trinker/topicmodels_learning#videos)Videos

## [](https://github.com/trinker/topicmodels_learning#introductory)Introductory

- Boyd-Graber, J. (2013). [Computational Linguistics I: Topic Modeling](https://www.youtube.com/watch?v=4p9MSJy761Y)

## [](https://github.com/trinker/topicmodels_learning#theory)Theory

## [](https://github.com/trinker/topicmodels_learning#visualization)Visualization

## [](https://github.com/trinker/topicmodels_learning#articles)Articles

## [](https://github.com/trinker/topicmodels_learning#applied)Applied

## [](https://github.com/trinker/topicmodels_learning#theoretical)Theoretical

- Blei, D. M. (2012). [Probabilistic topic models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Blei2012.pdf). _Communications of the ACM, (55)_4, 77-84\. doi:10.1145/2133806.2133826
- Blei, D. M. & Lafferty, J. D. (2007) [A correlated topic model of Science](https://github.com/trinker/topicmodels_learning/blob/master/articles/Blei2007.pdf). _The Annals of Applied Statistics 1_(1), 17-35\. doi:10.1214/07-AOAS114
- Blei, D. M. & Lafferty, J. D. (2009) [Topic models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Blei2009.pdf). In A Srivastava, M Sahami (eds.), [_Text mining: classification, clustering, and applications_](https://github.com/trinker/topicmodels_learning/blob/master/articles/Srivastava2009.pdf). Chapman & Hall/CRC Press. 71-93\.
- Blei, D. M. & McAuliffe, J. (2008). [Supervised topic models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Blei2008.pdf). In Advances in Neural Information Processing Systems 20, 1-8.
- Blei, D. M., Ng, A.Y., & Jordan, M.I. (2003). [Latent Dirichlet Allocation](https://github.com/trinker/topicmodels_learning/blob/master/articles/Blei2003.pdf). _Journal of Machine Learning Research, 3_, 993-1022.
- Chang, J., Boyd-Graber, J. , Wang, C., Gerrish, S., & Blei. D. (2009). [Reading tea leaves: How humans interpret topic models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Chang2009.pdf). In _Neural Information Processing Systems_.
- Griffiths, T.L. & Steyvers, M. (2004). [Finding Scientific Topics](https://github.com/trinker/topicmodels_learning/blob/master/articles/Griffiths2004.pdf). Proceedings of the National Academy of Sciences of the United States of America, 101, 5228-5235.
- Griffiths, T.L., Steyvers, M., & Tenenbaum, J.B.T. (2007). [Topics in Semantic Representation](https://github.com/trinker/topicmodels_learning/blob/master/articles/Griffiths2007.pdf). _Psychological Review, 114_(2), 211-244.
- Grüen, B. & Hornik, K. (2011). [topicmodels: An R Package for Fitting Topic Models.](https://github.com/trinker/topicmodels_learning/blob/master/articles/Gruen2011.pdf). _Journal of Statistical Software, 40_(13), 1-30.
- Mimno, D. & A. Mccallum. (2007). [Organizing the OCA: learning faceted subjects from a library of digital books](https://github.com/trinker/topicmodels_learning/blob/master/articles/Mimno2007.pdf). In _Joint Conference on Digital Libraries_. ACM Press, New York, NY, 376â€"385.
- Ponweiser, M. (2012). [Latent Dirichlet Allocation in R (Diploma Thesis)](https://github.com/trinker/topicmodels_learning/blob/master/articles/Ponweiser2012.pdf). Vienna University of Economics and Business, Vienna
- Roberts M.E., Stewart B.M., Tingley D., & Airoldi E.M. (2013) [The Structural Topic Model and Applied Social Science](https://github.com/trinker/topicmodels_learning/blob/master/articles/Roberts2013.pdf). _Advances in Neural Information Processing Systems Workshop on Topic Models: Computation, Application, and Evaluation_, 1-4\.
- Roberts, M., Stewart, B., Tingley, D., Lucas, C., Leder-Luis, J., Gadarian, S., Albertson, B., et al. (2014). [Structural topic models for open ended survey responses](https://github.com/trinker/topicmodels_learning/blob/master/articles/Roberts2014.pdf). _American Journal of Political Science, American Journal of Political Science, 58_(4), 1064-1082.
- Roberts, M., Stewart, B., Tingley, D. (n.d.). [stm: R Package for Structural Topic Models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Robertsnd.pdf), 1-49.
- Sievert, C. & Shirley, K. E. (2014a). [LDAvis: A Method for Visualizing and Interpreting Topics.](https://github.com/trinker/topicmodels_learning/blob/master/articles/Sievert2014a.pdf) in _Proceedings of the Workshop on Interactive Language Learning, Visualization, and Interfaces_ 63-70.
- Steyvers, M. & Griffiths, T. (2007). [Probabilistic topic models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Steyvers2007.pdf). In T. Landauer, D McNamara, S. Dennis, and W. Kintsch (eds), _Latent Semantic Analysis: A Road to Meaning_. Laurence Erlbaum
- Taddy, M.A. (2012). [On Estimation and Selection for Topic Models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Taddy2012.pdf) In _Proceedings of the 15th International Conference on Artificial Intelligence and Statistics (AISTATS 2012)_, 1184-1193.
- Tang, J., Meng, Z., Nguyen, X. , Mei, Q. , & Zhang, M. (2014). [Understanding the limiting factors of topic modeling via posterior contraction analysis](https://github.com/trinker/topicmodels_learning/blob/master/articles/Tang2014.pdf). In _31 st International Conference on Machine Learning_, 190-198.

## [](https://github.com/trinker/topicmodels_learning#websites--blogs)Websites & Blogs

- Blei, D. (n.d.). [Topic Modeling](https://www.cs.princeton.edu/%7Eblei/topicmodeling.html)
- Jockers, M.L. (2013). ["Secret" Recipe for Topic Modeling Themes](http://www.matthewjockers.net/2013/04/12/secret-recipe-for-topic-modeling-themes/)
- Jones, T. (n.d.). [Topic Models Reading List](http://www.biasedestimates.com/p/topic-models-reading-list.html)
- Marwick, B. (2014a). [The input parameters for using latent Dirichlet allocation](http://stats.stackexchange.com/a/25128/7482)
- Marwick, B. (2014b). [Topic models: cross validation with loglikelihood or perplexity](http://stackoverflow.com/a/21394092/1000343)
- Rhody, L. M. (2012). [Some Assembly Required: Understanding and Interpreting Topics in LDA Models of Figurative Language](http://www.lisarhody.com/some-assembly-required)
- Schmidt, B.M. (2012). [Words Alone: Dismantling Topic Models in the Humanities](http://journalofdigitalhumanities.org/2-1/words-alone-by-benjamin-m-schmidt/)
- Underwood, T. (2012a). [Topic Modeling Made Just Simple Enough](http://tedunderwood.com/2012/04/07/topic-modeling-made-just-simple-enough/)
- Underwood, T. (2012b). [What kinds of "topics" does topic modeling actually produce?](http://tedunderwood.com/2012/04/01/what-kinds-of-topics-does-topic-modeling-actually-produce/)
- Weingart, S. (2012). [Topic Modeling for Humanists: A Guided Tour](http://www.scottbot.net/HIAL/?p=19113)
- Weingart, S. (2011). [Topic Modeling and Network Analysis](http://www.scottbot.net/HIAL/?p=221)

## [](https://github.com/trinker/topicmodels_learning#r-resources)R Resources

## [](https://github.com/trinker/topicmodels_learning#package-comparisons)Package Comparisons

Package     | Functionality             | Pluses                                                                                    | Author                      | R Language Interface
----------- | ------------------------- | ----------------------------------------------------------------------------------------- | --------------------------- | ---------------------------------------------------------
lda__       | Collapsed Gibbs for LDA   | Graphing utilities                                                                        | Chang                       | R
topicmodels | LDA and CTM               | Follows Blei's implementation; great vignette; takes                                      | C                           | [DTM](https://en.wikipedia.org/wiki/Document-term_matrix)
stm         | Model w/ meta-data        | Great documentation; nice visualization                                                   | Roberts, Stewart, & Tingley | C
LDAvis      | Interactive visualization | Aids in model interpretation                                                              | Sievert & Shirley           | R + Shiny
mallet*     | LDA                       | [MALLET](http://programminghistorian.org/lessons/topic-modeling-and-mallet) is well known | Mimno                       | Java

*[_StackExchange discussion of lda vs. topicmodels_](http://stats.stackexchange.com/questions/24441/two-r-packages-for-topic-modeling-lda-and-topicmodels)<br>
**[_Setting Up MALLET_](http://programminghistorian.org/lessons/topic-modeling-and-mallet)

## [](https://github.com/trinker/topicmodels_learning#r-specific-references)R Specific References

- Chang J. (2010). lda: Collapsed Gibbs Sampling Methods for Topic Models. <http://CRAN.R-project.org/package=lda>.
- Grüen, B. & Hornik, K. (2011). [topicmodels: An R Package for Fitting Topic Models.](https://github.com/trinker/topicmodels_learning/blob/master/articles/Gruen2011.pdf). _Journal of Statistical Software, 40_(13), 1-30.
- Mimno, D. (2013). [vignette-mallet: A wrapper around the Java machine learning tool MALLET](https://github.com/trinker/topicmodels_learning/blob/master/articles/Mimno2013.Rmd). <https://CRAN.R-project.org/package=mallet>
- Ponweiser, M. (2012). [Latent Dirichlet Allocation in R (Diploma Thesis)](https://github.com/trinker/topicmodels_learning/blob/master/articles/Ponweiser2012.pdf). Vienna University of Economics and Business, Vienna.
- Roberts, M., Stewart, B., Tingley, D. (n.d.). [stm: R Package for Structural Topic Models](https://github.com/trinker/topicmodels_learning/blob/master/articles/Robertsnd.pdf), 1-49.
- Sievert, C. & Shirley, K. E. (2014a). [LDAvis: A Method for Visualizing and Interpreting Topics.](https://github.com/trinker/topicmodels_learning/blob/master/Sievert2014a.pdf) _Proceedings of the Workshop on Interactive Language Learning, Visualization, and Interfaces_ 63-70.
- Sievert, C. & Shirley, K. E. (2014b). [Vignette: LDAvis details.](https://github.com/trinker/topicmodels_learning/blob/master/articles/Sievert2014b.pdf) 1-5.

## [](https://github.com/trinker/topicmodels_learning#example-modeling)Example Modeling

## [](https://github.com/trinker/topicmodels_learning#topic-modeling-r-demo)Topic Modeling R Demo

## [](https://github.com/trinker/topicmodels_learning#topicmodels-package)topicmodels Package

The .R script for this demonstration can be downloaded from [scripts/Example_topic_model_analysis.R](https://raw.githubusercontent.com/trinker/topicmodels_learning/master/scripts/Example_topic_model_analysis.R)

### [](https://github.com/trinker/topicmodels_learning#installload-tools--data)Install/Load Tools & Data

```
if (!require("pacman")) install.packages("pacman")
pacman::p_load_gh("trinker/gofastr")
pacman::p_load(tm, topicmodels, dplyr, tidyr, igraph, devtools, LDAvis, ggplot2)

## Source topicmodels2LDAvis & optimal_k functions
invisible(lapply(
 file.path(
 "https://raw.githubusercontent.com/trinker/topicmodels_learning/master/functions", 
 c("topicmodels2LDAvis.R", "optimal_k.R")
 ),
 devtools::source_url
))

## SHA-1 hash of file is 5ac52af21ce36dfe8f529b4fe77568ced9307cf0
## SHA-1 hash of file is 7f0ab64a94948c8b60ba29dddf799e3f6c423435

data(presidential_debates_2012)
```

### [](https://github.com/trinker/topicmodels_learning#generate-stopwords)Generate Stopwords

```
stops <- c(
 tm::stopwords("english"),
 tm::stopwords("SMART"),
 "governor", "president", "mister", "obama","romney"
 ) %>%
 gofastr::prep_stopwords() 
```

### [](https://github.com/trinker/topicmodels_learning#create-the-documenttermmatrix)Create the DocumentTermMatrix

```
doc_term_mat <- presidential_debates_2012 %>%
 with(gofastr::q_dtm_stem(dialogue, paste(person, time, sep = "_"))) %>% 
 gofastr::remove_stopwords(stops, stem=TRUE) %>% 
 gofastr::filter_tf_idf() %>%
 gofastr::filter_documents() 
```

### [](https://github.com/trinker/topicmodels_learning#control-list)Control List

```
control <- list(burnin = 500, iter = 1000, keep = 100, seed = 2500)
```

### [](https://github.com/trinker/topicmodels_learning#determine-optimal-number-of-topics)Determine Optimal Number of Topics

The plot below shows the harmonic mean of the log likelihoods against k (number of topics).

```
(k <- optimal_k(doc_term_mat, 40, control = control))

## 
## Grab a cup of coffee this could take a while...

## 10 of 40 iterations (Current: 08:54:32; Elapsed: .2 mins)
## 20 of 40 iterations (Current: 08:55:07; Elapsed: .8 mins; Remaining: ~2.3 mins)
## 30 of 40 iterations (Current: 08:56:03; Elapsed: 1.7 mins; Remaining: ~1.3 mins)
## 40 of 40 iterations (Current: 08:57:30; Elapsed: 3.2 mins; Remaining: ~0 mins)
## Optimal number of topics = 20
```

[![](https://github.com/trinker/topicmodels_learning/raw/master/inst/figure/unnamed-chunk-7-1.png)](https://github.com/trinker/topicmodels_learning/blob/master/inst/figure/unnamed-chunk-7-1.png)

It appears the optimal number of topics is ~k = 20.

### [](https://github.com/trinker/topicmodels_learning#run-the-model)Run the Model

```
control[["seed"]] <- 100
lda_model <- topicmodels::LDA(doc_term_mat, k=as.numeric(k), method = "Gibbs", 
 control = control)
```

### [](https://github.com/trinker/topicmodels_learning#plot-the-topics-per-person--time)Plot the Topics Per Person & Time

```
topics <- topicmodels::posterior(lda_model, doc_term_mat)[["topics"]]
topic_dat <- dplyr::add_rownames(as.data.frame(topics), "Person_Time")
colnames(topic_dat)[-1] <- apply(terms(lda_model, 10), 2, paste, collapse = ", ")

tidyr::gather(topic_dat, Topic, Proportion, -c(Person_Time)) %>%
 tidyr::separate(Person_Time, c("Person", "Time"), sep = "_") %>%
 dplyr::mutate(Person = factor(Person, 
 levels = c("OBAMA", "ROMNEY", "LEHRER", "SCHIEFFER", "CROWLEY", "QUESTION" ))
 ) %>%
 ggplot2::ggplot(ggplot2::aes(weight=Proportion, x=Topic, fill=Topic)) +
 ggplot2::geom_bar() +
 ggplot2::coord_flip() +
 ggplot2::facet_grid(Person~Time) +
 ggplot2::guides(fill=FALSE) +
 ggplot2::xlab("Proportion")
```

[![](https://github.com/trinker/topicmodels_learning/raw/master/inst/figure/unnamed-chunk-9-1.png)](https://github.com/trinker/topicmodels_learning/blob/master/inst/figure/unnamed-chunk-9-1.png)

### [](https://github.com/trinker/topicmodels_learning#plot-the-topics-matrix-as-a-heatmap)Plot the Topics Matrix as a Heatmap

```
heatmap(topics, scale = "none")
```

[![](https://github.com/trinker/topicmodels_learning/raw/master/inst/figure/unnamed-chunk-10-1.png)](https://github.com/trinker/topicmodels_learning/blob/master/inst/figure/unnamed-chunk-10-1.png)

### [](https://github.com/trinker/topicmodels_learning#network-of-the-word-distributions-over-topics-topic-relation)Network of the Word Distributions Over Topics (Topic Relation)

```
post <- topicmodels::posterior(lda_model)

cor_mat <- cor(t(post[["terms"]]))
cor_mat[ cor_mat < .05 ] <- 0
diag(cor_mat) <- 0

graph <- graph.adjacency(cor_mat, weighted=TRUE, mode="lower")
graph <- delete.edges(graph, E(graph)[ weight < 0.05])

E(graph)$edge.width <- E(graph)$weight*20
V(graph)$label <- paste("Topic", V(graph))
V(graph)$size <- colSums(post[["topics"]]) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 15

par(mar=c(0, 0, 3, 0))
set.seed(110)
plot.igraph(graph, edge.width = E(graph)$edge.width, 
 edge.color = "orange", vertex.color = "orange", 
 vertex.frame.color = NA, vertex.label.color = "grey30")
title("Strength Between Topics Based On Word Probabilities", cex.main=.8)
```

[![](https://github.com/trinker/topicmodels_learning/raw/master/inst/figure/unnamed-chunk-11-1.png)](https://github.com/trinker/topicmodels_learning/blob/master/inst/figure/unnamed-chunk-11-1.png)

### [](https://github.com/trinker/topicmodels_learning#network-of-the-topics-over-dcouments-topic-relation)Network of the Topics Over Dcouments (Topic Relation)

```
minval <- .1
topic_mat <- topicmodels::posterior(lda_model)[["topics"]]

graph <- graph_from_incidence_matrix(topic_mat, weighted=TRUE)
graph <- delete.edges(graph, E(graph)[ weight < minval])

E(graph)$edge.width <- E(graph)$weight*17
E(graph)$color <- "blue"
V(graph)$color <- ifelse(grepl("^\\d+$", V(graph)$name), "grey75", "orange")
V(graph)$frame.color <- NA
V(graph)$label <- ifelse(grepl("^\\d+$", V(graph)$name), paste("topic", V(graph)$name), gsub("_", "\n", V(graph)$name))
V(graph)$size <- c(rep(10, nrow(topic_mat)), colSums(topic_mat) articles _articles bin CAs devops Documents dotfiles gethtml go hnews jason js netdata start-thesrc thesrc ucii 20)
V(graph)$label.color <- ifelse(grepl("^\\d+$", V(graph)$name), "red", "grey30")

par(mar=c(0, 0, 3, 0))
set.seed(369)
plot.igraph(graph, edge.width = E(graph)$edge.width, 
 vertex.color = adjustcolor(V(graph)$color, alpha.f = .4))
title("Topic & Document Relationships", cex.main=.8)
```

[![](https://github.com/trinker/topicmodels_learning/raw/master/inst/figure/unnamed-chunk-12-1.png)](https://github.com/trinker/topicmodels_learning/blob/master/inst/figure/unnamed-chunk-12-1.png)

### [](https://github.com/trinker/topicmodels_learning#ldavis-of-model)LDAvis of Model

The output from **LDAvis** is not easily embedded within an R markdown document, however, the reader may [see the results here](http://trinker.github.io/LDAvis/example/).

```
lda_model %>%
 topicmodels2LDAvis() %>%
 LDAvis::serVis()
```

### [](https://github.com/trinker/topicmodels_learning#apply-model-to-new-data)Apply Model to New Data

```
## Create the DocumentTermMatrix for New Data
doc_term_mat2 <- partial_republican_debates_2015 %>%
 with(gofastr::q_dtm_stem(dialogue, paste(person, location, sep = "_"))) %>% 
 gofastr::remove_stopwords(stops, stem=TRUE) %>% 
 gofastr::filter_tf_idf() %>%
 gofastr::filter_documents() 


## Update Control List
control2 <- control
control2[["estimate.beta"]] <- FALSE


## Run the Model for New Data
lda_model2 <- topicmodels::LDA(doc_term_mat2, k = k, model = lda_model, 
 control = list(seed = 100, estimate.beta = FALSE))


## Plot the Topics Per Person & Location for New Data
topics2 <- topicmodels::posterior(lda_model2, doc_term_mat2)[["topics"]]
topic_dat2 <- dplyr::add_rownames(as.data.frame(topics2), "Person_Location")
colnames(topic_dat2)[-1] <- apply(terms(lda_model2, 10), 2, paste, collapse = ", ")

tidyr::gather(topic_dat2, Topic, Proportion, -c(Person_Location)) %>%
 tidyr::separate(Person_Location, c("Person", "Location"), sep = "_") %>%
 ggplot2::ggplot(ggplot2::aes(weight=Proportion, x=Topic, fill=Topic)) +
 ggplot2::geom_bar() +
 ggplot2::coord_flip() +
 ggplot2::facet_grid(Person~Location) +
 ggplot2::guides(fill=FALSE) +
 ggplot2::xlab("Proportion")


## LDAvis of Model for New Data
lda_model2 %>%
 topicmodels2LDAvis() %>%
 LDAvis::serVis()
```

## [](https://github.com/trinker/topicmodels_learning#contributing)Contributing

You are welcome to:
