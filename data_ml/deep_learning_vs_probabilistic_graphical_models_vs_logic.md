# Deep Learning vs Probabilistic Graphical Models vs Logic

[Original URL](http://www.computervisionblog.com/2015/04/deep-learning-vs-probabilistic.html)

> Today, let's take a look at three paradigms that have shaped the field of Artificial Intelligence in the last 50 years: Logic, Probabilistic Methods, and Deep Learning. The empirical,...

Today, let's take a look at three paradigms****that have shaped the field of Artificial Intelligence in the last 50 years: **Logic**, **Probabilistic Methods**, and **Deep Learning**. The empirical, "data-driven", or big-data / deep-learning ideology triumphs today, but that wasn't always the case. Some of the earliest approaches to AI were based on Logic, and the transition from logic to data-driven methods has been heavily influenced by probabilistic thinking, something we will be investigating in this blog post.

Let's take a look back Logic and Probabilistic Graphical Models and make some predictions on where the field of AI and Machine Learning is likely to go in the near future. We will proceed in chronological order.

[![](http://3.bp.blogspot.com/-b_Ux2LXhPyk/VSVdqYcp6-I/AAAAAAAAN8M/eBJ2ln-6nDU/s1600/probgraphmods.png)](http://3.bp.blogspot.com/-b_Ux2LXhPyk/VSVdqYcp6-I/AAAAAAAAN8M/eBJ2ln-6nDU/s1600/probgraphmods.png)

Image from Coursera's [Probabilistic Graphical Models](http://online.stanford.edu/pgm-fa12) course

## **1\. Logic and Algorithms (Common-sense "Thinking" Machines)**

A lot of early work on Artificial Intelligence was concerned with Logic, Automated Theorem Proving, and manipulating symbols. It should not be a surprise that John McCarthy's seminal 1959 paper on AI had the title "Programs with common sense."

If we peek inside one of most popular AI textbooks, namely "Artificial Intelligence: A Modern Approach," we immediately notice that the beginning of the book is devoted to **search, constraint satisfaction problems, first order logic, and planning**. The third edition's cover (pictured below) looks like a big chess board (because being good at chess used to be a sign of human intelligence), features a picture of Alan Turing (the father of computing theory) as well as a picture of Aristotle (one of the greatest classical philosophers which had quite a lot to say about intelligence).

[![](http://1.bp.blogspot.com/-BCOXrp3r-SI/VSVYsgEm-eI/AAAAAAAAN74/J2Lx0ssKvsA/s1600/cover2.jpg)](http://1.bp.blogspot.com/-BCOXrp3r-SI/VSVYsgEm-eI/AAAAAAAAN74/J2Lx0ssKvsA/s1600/cover2.jpg)

The cover of [AIMA](http://aima.cs.berkeley.edu/), the canonical AI text for undergraduate CS students

Unfortunately, logic-based AI brushes the perception problem under the rug, and I've argued quite some time ago that understanding how perception works is really the key to unlocking the secrets of intelligence. Perception is one of those things which is easy for humans and immensely difficult for machines. (To read more see my 2011 blog post, [Computer Vision is Artificial Intelligence](http://quantombone.blogspot.com/2011/03/computer-vision-is-artificial.html)). Logic is pure and traditional chess-playing bots are very algorithmic and search-y, but the real world is ugly, dirty, and ridden with uncertainty.

**I think most contemporary AI researchers agree that Logic-based AI is dead.** The kind of world where everything can be perfectly observed, a world with no measurement error, is not the world of robotics and big-data. We live in the era of machine learning, and numerical techniques triumph over first-order logic. As of 2015, I pity the fool who prefers Modus Ponens over Gradient Descent.

Logic is great for the classroom and I suspect that once enough perception problems become "essentially solved" that we will see a resurgence in Logic. And while there will be plenty of open perception problems in the future, there will be scenarios where the community can stop worrying about perception and start revisiting these classical ideas. Perhaps in 2020\.

**Further reading:** [Logic and Artificial Intelligence from the Stanford Encyclopedia of Philosophy](http://plato.stanford.edu/entries/logic-ai/)

## **2\. Probability, Statistics, and Graphical Models ("Measuring" Machines)**

Probabilistic methods in Artificial Intelligence came out of the need to deal with uncertainty. The middle part of the Artificial Intelligence a Modern Approach textbook is called "Uncertain Knowledge and Reasoning" and is a great introduction to these methods. If you're picking up AIMA for the first time, I recommend you start with this section. And if you're a student starting out with AI, do yourself a favor and don't skimp on the math.

[![](http://3.bp.blogspot.com/-TKWy6zvuPo4/VSVcc67N3YI/AAAAAAAAN8E/eSdTsH46L1A/s1600/HamburgerDensity4.gif)](http://3.bp.blogspot.com/-TKWy6zvuPo4/VSVcc67N3YI/AAAAAAAAN8E/eSdTsH46L1A/s1600/HamburgerDensity4.gif)

[Intro to PDFs](https://onlinecourses.science.psu.edu/stat414/node/97) from Penn State's Probability Theory and Mathematical Statistics course

When most people think about probabilistic methods they think of counting. In laymen's terms it's fair to think of probabilistic methods as fancy counting methods. Let's briefly take a look at what used to be the two competing methods for thinking probabilistically.

**Frequentist methods** are very empirical – these methods are data-driven and make inferences purely from data. **Bayesian methods** are more sophisticated and combine data-driven likelihoods with magical priors. These priors often come from first principles or "intuitions" and the Bayesian approach is great for combining heuristics with data to make cleverer algorithms – a nice mix of the rationalist and empiricist world views.

What is perhaps more exciting that then Frequentist vs. Bayesian flamewar, is something known as Probabilistic Graphical Models. This class of techniques comes from computer science, and even though Machine Learning is now a strong component of a CS and a Statistics degree, the true power of statistics only comes when it is married with computation.

**Probabilistic Graphical Models are a marriage of Graph Theory with Probabilistic Methods** and they were all the rage among Machine Learning researchers in the mid 2000s. Variational methods, Gibbs Sampling, and Belief Propagation were being pounded into the brains of CMU graduate students when I was in graduate school (2005-2011) and provided us with a superb mental framework for thinking about machine learning problems. I learned most of what I know about Graphical Models from [Carlos Guestrin](http://homes.cs.washington.edu/~guestrin/) and [Jonathan Huang](http://jonathan-huang.org/). Carlos Guestrin is now the CEO of GraphLab, Inc (now known as [Dato](https://dato.com/)) which is a company that builds large scale products for machine learning on graphs and Jonathan Huang is a senior research scientist at Google.

The video below is a high level overview of GraphLab, but it serves a very nice overview of "graphical thinking" and how it fits into the modern data scientist's tool-belt. Carlos is an excellent lecturer and his presentation is less about the company's product and more about ways for thinking about next generation machine learning systems.

A Computational Introduction to Probabilistic Graphical Models by GraphLab, Inc CEO Prof. Carlos Guestrin<br>
If you think that deep learning is going to solve all of your machine learning problems, you should really take a look at the above video. If you're building recommender systems, an analytics platform for healthcare data, designing a new trading algorithm, or building the next generation search engine, Graphical Models are perfect place to start.

**Further reading:**<br>
[Belief Propagation Algorithm Wikipedia Page](http://en.wikipedia.org/wiki/Belief_propagation)<br>
[An Introduction to Variational Methods for Graphical Models](http://www.cs.berkeley.edu/~jordan/papers/variational-intro.pdf) by Michael Jordan et al.<br>
[Michael Jordan's webpage](http://www.cs.berkeley.edu/~jordan/) (one of the titans of inference and graphical models)

## **3\. Deep Learning and Machine Learning (Data-Driven Machines)**

Machine Learning is about learning from examples and today's state-of-the-art recognition techniques require a lot of training data, a deep neural network, and patience. **Deep Learning emphasizes the network architecture of today's most successful machine learning approaches.** These methods are based on "deep" multi-layer neural networks with many hidden layers. NOTE: I'd like to emphasize that using deep architectures (as of 2015) is not new. Just check out the following "deep" architecture from 1998\.

[![](http://4.bp.blogspot.com/-eAFL2rN9cm0/VRjBiJiecrI/AAAAAAAAN4E/2Q7LthaoLEY/s1600/lenet.png)](http://4.bp.blogspot.com/-eAFL2rN9cm0/VRjBiJiecrI/AAAAAAAAN4E/2Q7LthaoLEY/s1600/lenet.png)

LeNet-5 Figure From Yann LeCun's seminal "

<span>Gradient-based learning</span>

<span>applied to document recognition"</span>

 paper.

When you take a look at [modern guide about LeNet](http://deeplearning.net/tutorial/lenet.html), it comes with the following disclaimer:

"To run this example on a GPU, you need a good GPU. It needs at least 1GB of GPU RAM. More may be required if your monitor is connected to the GPU.

When the GPU is connected to the monitor, there is a limit of a few seconds for each GPU function call. This is needed as current GPUs can't be used for the monitor while doing computation. Without this limit, the screen would freeze for too long and make it look as if the computer froze. This example hits this limit with medium-quality GPUs. When the GPU isn't connected to a monitor, there is no time limit. You can lower the batch size to fix the time out problem."

It really **makes me wonder how Yann was able to get _anything_ out of his deep model back in 1998**. Perhaps it's not surprising that it took another decade for the rest of us to get the memo.

UPDATE: Yann pointed out (via a Facebook comment) that the ConvNet work dates back to 1989\. "It had about 400K connections and took about 3 weeks to train on the USPS dataset (8000 training examples) on a SUN4 machine." – LeCun

[![](http://4.bp.blogspot.com/-O60saG4CJs4/VSX5HjoXr4I/AAAAAAAAN8o/smmT02ecM_k/s1600/1989net.jpg)](http://4.bp.blogspot.com/-O60saG4CJs4/VSX5HjoXr4I/AAAAAAAAN8o/smmT02ecM_k/s1600/1989net.jpg)

[A Deep Network from Yann's work at Bell Labs from 1989](http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf)

NOTE: At roughly the same time (~1998) two crazy guys in California were trying to cache the entire internet inside the computers in their garage (they started some funny-sounding company which starts with a G). I don't know how they did it, but I guess sometimes to win big you have to [do things that don't scale](http://paulgraham.com/ds.html). Eventually the world will catch up.

**Further reading:**<br>

<span>Y. LeCun, L. Bottou, Y. Bengio, and P. Haffner. <a href="http://yann.lecun.com/exdb/publis/pdf/lecun-98.pdf">Gradient-based learning applied to document recognition</a>. </span>

Proceedings of the IEEE

<span>, November 1998.</span>

<br>

<span>
  <br>
</span>

 

<span>Y. LeCun, B. Boser, J. S. Denker, D. Henderson, R. E. Howard, W. Hubbard and L. D. Jackel: <a href="http://yann.lecun.com/exdb/publis/pdf/lecun-89e.pdf">Backpropagation Applied to Handwritten Zip Code Recognition</a>, Neural Computation, 1(4):541-551, Winter 1989</span>

<br>

<span>
  <br>
</span>

<span><strong>Deep Learning code:</strong> Modern <a href="http://deeplearning.net/tutorial/lenet.html">LeNet implementation in Theano</a>and docs.</span>

<br>

<span>
  <br>
</span>

## <span>
  <strong>Conclusion</strong>
</span>

<span>I don’t see traditional first-order logic making a comeback anytime soon. And while there is a lot of hype behind deep learning, distributed systems and “graphical thinking” is likely to make a much more profound impact on data science than heavily optimized CNNs. There is no reason why deep learning can’t be combined with a GraphLab-style architecture, and some of the new exciting machine learning work in the next decade is likely to be a marriage of these two philosophies.</span>

You can also check out a relevant post from last month:<br>
[Deep Learning vs Machine Learning vs Pattern Recognition](http://quantombone.blogspot.com/2015/03/deep-learning-vs-machine-learning-vs.html)

[Discuss on Hacker News]()
