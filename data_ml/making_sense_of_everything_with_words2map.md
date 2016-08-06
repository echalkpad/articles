# Making Sense of Everything with words2map

[Original URL](http://blog.yhat.com/posts/words2map.html)

> We are now at a point in history when algorithms can learn, like people, about pretty much anything. Facebook is pursuing "computer services that have better perception than people",...

We are now at a point in history when algorithms can learn, like people, about pretty much anything. Facebook is pursuing ["computer services that have better perception than people"](http://overlap.ai/facebook-computer-services-better-perception-than-people), while researchers at Google aim to "[solve intelligence](http://overlap.ai/google-solve-intelligence)". At [overlap.ai](http://overlap.ai/words2map) we're building artificial intelligence to unite people through their overlapping passions, and here we introduce a framework we call [words2map](http://overlap.ai/words2map-github) for considering what our users love, like these personal passions of ours:

![](http://blog.yhat.com/static/img/words2map-1.png)

Let's explain [the code](http://overlap.ai/words2map-github) that made this pretty picture just from the raw text, which serves as the basis for our recommender system. When we set out to automatically recommend groups and events to people that they may wish to join, we didn't have any data to start with, and yet the [unreasonable effectiveness](http://overlap.ai/google-unreasonable-effectiveness-of-data) of data was not lost on us. So we decided to build from a pre-trained machine learning model that has basically already seen all there is to see: we set up a [word2vec model from Google trained on 100 billion words](http://overlap.ai/word2vec-model) -- just a few orders of magnitude bigger than Wikipedia. And indeed we found amazing insights embedded in the vectors, like:

- _human + robot ≈ **cyborg**_
- _electricity + silicon ≈ **solar cells**_
- _virtual reality + reality ≈ **augmented reality**_

Minus the futurism fetish above, you'll see the math above is fairly simple. We call this "adding vectors", while below you see what we are really doing is averaging each element across all the vectors. Let's dissect the guts here, since deft applications of the vector algebra will take us far in terms of hacking intelligence into and out of the system:

![](http://blog.yhat.com/static/img/adding-vectors.png)

The key point above is that new vectors are simply nearby existing vectors, such that _king - man + woman ≠ **queen**_ (at least not exactly). From this, it is clear that any new words can be introduced to the model simply by adding existing vectors for existing words. This idea is similar to how humans add words to their own vocabulary with a dictionary: consider some familiar words and combine their meanings to figure out new words. Our A.I. responds to unknown words by searching the web for them, extracting keywords from relevant websites, and adding vectors for those keywords it knows, to produce a new vector representing the new thing. This feels like it shouldn't work, like becoming a scientist just by reading Wikipedia instead of doing real science experiments, but surprisingly its performance is robust. Here is another cool map from [words2map](http://overlap.ai/words2map-github) (which you can easily download and play with yourself) that demonstrates this:

![](http://blog.yhat.com/static/img/words2map-2.png)

Once each word is derived in 300 dimensions (from the word2vec model trained on 100 billion words) we then applied t-SNE to embed them into 2D, as x and y coordinates for data visualization, like seen above. This is indeed nice for data visualization, while it's also very helpful in our pipeline because it removes noise in the derived vectors, by forcing a new mapping based purely on relative similarity. For this reason we will be using the low-dimensional coordinates of each word in our recommender system.

The above pipeline is strong enough for many applications, but in our case we wanted to go further to be able to uncover clusters of similar types of activities that our users really enjoy (and also, to be able to quickly infer what types of things you _don't_ like). That way, we can recommend more or less of these things to you, and do so with high precision purely through A.I. At this point in our research we tried balltrees as a way to identify clusters:

![](http://blog.yhat.com/static/img/t-SNE-clusters.png)

Upon close examination, these clusters are not bad, but we felt they weren't as responsive to the topology of the underlying data as we'd like. So we continued researching and found a clustering algorithm that works really well for our complex distributions: HDBSCAN, i.e. "hierarchical density-based spatial clustering of applications with noise". It sounds fancy, but it's very simple to work with, given that you only need to put in 2D coordinates.

The full pipeline for all of this word vector hackery comes together like so:

![](http://blog.yhat.com/static/img/words2map-pipeline.png)

We're very happy to make [words2map available through github](http://blog.yhat.com/posts/overlap.ai/words2map-github), and have worked hard to make sure that almost anyone with a Mac or Linux terminal can quickly download and play with it by copying and pasting:

```
git clone https://github.com/overlap-ai/words2map.git
cd words2map
./install.sh
```

We welcome critical feedback, and anyone interested in joining us in advancing the state of this art across machine learning + data visualization, to make it better and better for everyone.

As of today, words2map is mapping every group that our users launch through our iPhone app -- which you can [download now](http://overlap.ai/download-overlap). We've tested it, and know it reliably derives reasonable vectors in an online way for any topic that's learnable on the web -- i.e. just about every topic. Readers are invited to [try out words2map](http://overlap.ai/words2map-github) for any type of natural language processing, and share their maps using a [#words2map](http://overlap.ai/words2map-hashtag) hashtag.

Readers are also invited to overlap with us over A.I. and data science​, in New York City and beyond​. This summer we're hosting coffee chats, data hackathon​s​, and other fun stuff so we can connect, join forces, and hack​ cool stuff​​ together​.​ Just [download the app here](http://overlap.ai/) and soon you'll be nerding with us IRL.

Big thanks to the data hipsters at [Yhat](http://overlap.ai/yhat) for helping us share something new on their blog, and for spearheading great open source data science tools like [Rodeo](http://overlap.ai/yhat-rodeo) and [ggplot](http://yhat.github.io/ggplot/). And thanks to so many scientists, engineers, and leaders who have helped make all of this possible. We love you:

![](http://blog.yhat.com/static/img/we-love-you.png)
