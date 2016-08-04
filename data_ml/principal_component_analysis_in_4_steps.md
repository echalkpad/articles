# Principal Component Analysis in 4 Steps

[Original URL](http://alexhwoods.com/pca/)

> Recently I wrote a couple posts about eigenvectors and eigenvalues. I thought it would be cool to go from that slightly more theoretical material and show something super useful in...

Recently I wrote a couple posts about [eigenvectors](http://alexhwoods.com/eigenvectors/) and [eigenvalues](http://alexhwoods.com/eigenvalues/). I thought it would be cool to go from that slightly more theoretical material and show something super useful in which eigenvectors / eigenvalues are an integral part. So today, I'm going to talk about Principal Component Analysis.

This algorithm is used everywhere, notably in [neuroscience](https://en.wikipedia.org/wiki/Principal_component_analysis#Neuroscience) and computer graphics. The idea is you have a dataset with a ton of features and you want to reduce it to it's core components. With high dimensionality, not only is the [curse of dimensionality](https://en.wikipedia.org/wiki/Curse_of_dimensionality) a problem, but you also just can't visualize the data, which prevents a lot of basic insights. So we want to reduce the dimensionality without losing vital information. This is where PCA comes in. In PCA, we go from a large number of features to a small number of components, which still contain a sufficient proportion of the information.

Before we talk about the algorithm itself, there are a few important math concepts which you must be familiar with in order to proceed. The first is eigenvalues and eigenvectors. You can read about them [here](http://alexhwoods.com/eigenvalues/). The second is the covariance matrix.

## Covariance Matrix

[Covariance](http://mathworld.wolfram.com/Covariance.html) is the measure of how two different variables change together. The covariance between two variables, X and Y, can be given by the following formula.

[![Screen Shot 2016-01-18 at 9.37.50 AM](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-18-at-9.37.50-AM.jpg?resize=238%2C53)](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-18-at-9.37.50-AM.jpg)

Now, if we wanted to look at all the possible covariances in a dataset, we can compute the covariance matrix, which has this form –

[![Screen Shot 2016-01-18 at 9.43.14 AM](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-18-at-9.43.14-AM.jpg?resize=264%2C72)](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Screen-Shot-2016-01-18-at-9.43.14-AM.jpg)

Notice that this matrix will be [symmetric](https://en.wikipedia.org/wiki/Symmetric_matrix) (A = A^T), and will have a diagonal of just variances, because cov(x, x) is the same thing as the variance of x. If you understand the covariance matrix and eigenvalues/vectors, you're ready to learn about PCA.

## Principal Component Analysis

Here is the central idea of PCA – **the components are the eigenvectors of the covariance matrix.** Moreover, the amount of variance each component captures can be represented by the magnitude of its corresponding eigenvalue. Read that multiple times.

So basically, PCA can be represented in 4 pretty simple steps.

1. **Normalize the data.** We're dealing with covariance, so it's a good idea to have features on the same scale.
2. **Calculate the covariance matrix.** 
3. **Find the eigenvectors of the covariance matrix.**
4. **Translate the data to be in terms of the components**. This involves just a simple matrix multiplication.

One more important thing to mention about this algorithm. We are going from _features_ to _components_. The translated dataset won't have a literal significance with respect to each component. It's very easy to digest what a _feature_ is--shoe size, weight, etc.--but a component we can only talk about from a mathematical perspective. If you want to gain a sense of understanding about a data point, you'll have to reference the original dataset.

## Example

Now I'll show you how to do PCA manually, and then using R's built-in function. In practice you'll usually use the built in function, but doing it manually is a great way to learn what's happening.

I found some data on the psychology within a large financial company. The dataset is called [attitude](https://stat.ethz.ch/R-manual/R-devel/library/datasets/html/attitude.html), and is accessible within R.

<span class="crayon-title">Get the data</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">library</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">MASS</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># install.packages(“MASS”) if you have to</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-c"># put the data in our workspace in R</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Ok, now let's do those 4 steps.

1. Normalize the data. This literally means put each feature on a normal curve. Just like you would calculate a z-score, subtract the mean and divide by the standard deviation, to the entire feature vector.

<span class="crayon-title">Step 1 - Normalize the data</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-r">attach</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># to save me having to type ‘attitude’ 20 times</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">rating</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">rating</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">rating</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">rating</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">complaints</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">complaints</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">complaints</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">complaints</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">privileges</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">privileges</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">privileges</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">privileges</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">learning</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">learning</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">learning</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">learning</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">raises</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">raises</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">raises</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">raises</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">critical</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">critical</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">critical</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">critical</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">advance</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">advance</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">mean</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">advance</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">/</span>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">advance</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-c"># re-attach so it calls the updated feature</span>
</p>
  <p>
  <span class="crayon-r">attach</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-e">summary</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># means are all 0</span>
</p>
  <p>
  <span class="crayon-e">sd</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">privileges</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># and sd’s are all 1 (you can check them all if you like)</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

1. Get the covariance matrix

<span class="crayon-title">Step 2 - The Covariance Matrix</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-p"># this is actually really simple..thanks R :)</span>
</p>
  <p>
  <span class="crayon-e">cov</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-p"># Quiz question - Why is the diagonal all 1’s? </span>
</p>
  <p>
  <span class="crayon-p"># Because we normalized each feature to have a variance of 1!</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

1. The Principal Components

Remember, the principal components are the eigenvectors of the covariance matrix.

<span class="crayon-title">Step 3 - The Principal Components</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">x</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">eigen</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-e">cov</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">x</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">vectors</span>
</p>
  <p>
  <span class="crayon-c"># just out of curiosity, I’m going to check that it did this right (%<em>% is matrix multiplication in R)</em></span>
</p>
  <em>
</em>
  <p>
  <em><span class="crayon-e">cov</span><span class="crayon-sy">(</span><span class="crayon-v">attitude</span><span class="crayon-sy">)</span><span class="crayon-o">%</span><span class="crayon-o">
</span></em>
  <span class="crayon-o">%</span>
  <span class="crayon-v">x</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">vectors</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-c"># gives the same values as…</span>
</p>
  <p>
  <span class="crayon-v">x</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">values</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">]</span>
  <span class="crayon-o">*</span>
  <span class="crayon-v">x</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">vectors</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-sy">]</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

1. Putting the data in terms of the components

We do this by matrix multiplying the transpose of the feature vector and the transpose of matrix containing the data. Why transpose? Theoretical reasons aside, the dimensions have to line up.

[![Step4Formula](http://i2.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Step4Formula.png?resize=300%2C95%20300w,%20http://i2.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Step4Formula.png?w=631%20631w)](http://i2.wp.com/alexhwoods.com/wp-content/uploads/2016/01/Step4Formula.png)

<span class="crayon-title">Step 4 - Translating the data</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">A</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">x</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">vectors</span>
  <span class="crayon-sy">[</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-cn">1</span>
  <span class="crayon-o">:</span>
  <span class="crayon-cn">3</span>
  <span class="crayon-sy">]</span>
</p>
  <p>
  <span class="crayon-v">B</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">data</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">matrix</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># because we can’t do matrix multiplication with data frames!</span>
</p>
  <p>
  <span class="crayon-c"># now we arrive at the new data by the above formula!</span>
</p>
  <p>
  <span class="crayon-v">newData</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-t">t</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">A</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">%</span>
  <span class="crayon-o">*</span>
  <span class="crayon-o">%</span>
  <span class="crayon-t">t</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">B</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

And then just a hint of data cleaning so we can have a nice data frame to work with and run algorithms on.

<span class="crayon-title">Some data cleaning</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-c"># note - in the newData matrix, each row is a feature, and each column is a data point. Let’s change that</span>
</p>
  <p>
  <span class="crayon-v">newData</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-t">t</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">newData</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">newData</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-v">data</span>
  <span class="crayon-sy">.</span>
  <span class="crayon-e">frame</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">newData</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-e">names</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">newData</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">c</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“feat1”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“feat2”</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-s">“feat3”</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

So, we successfully reduced a dataset of 7 features down to 3, maintaining a large amount of the information. But why did I choose 3? How do you know how many components to take?

Recall that the magnitude of the eigenvalue (corresponding to each eigenvector) tells us how much variance is represented by each component. Knowing this, we can graph the proportion of variance captured in each component, and decide how much is sufficient. I would advise a maximum of 3, because after that you can't visualize them all, and that's one of the reasons we did this in the first place!

[![EigenvaluePlot1](http://i0.wp.com/alexhwoods.com/wp-content/uploads/2016/01/EigenvaluePlot1.jpeg?resize=438%2C320)](http://i0.wp.com/alexhwoods.com/wp-content/uploads/2016/01/EigenvaluePlot1.jpeg)

The first two components capture 70% of the variance, and the third captures another 11%. I'm definitely comfortable reducing down to 3 dimensions while maintaining 81% of the information.

And our data looks like this, with respect to the components. At this point, I would start [clustering](https://en.wikipedia.org/wiki/Cluster_analysis).

[![DataAfterPCA](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2016/01/DataAfterPCA.jpeg?resize=411%2C329)](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2016/01/DataAfterPCA.jpeg)

## PCA Using R's Built-in Function

R has a built-in 'prcomp' function for PCA. It's really straightforward.

<span class="crayon-title">Using prcomp</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-c"># Extra Step - How To Do PCA Automatically in R (prcomp)</span>
</p>
  <p>
  <span class="crayon-c"># Don’t forget to scale the data, or set scale = True</span>
</p>
  <p>
  <span class="crayon-e">prcomp</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-c"># does it look like the eigenvectors from before? It should! Note - if some vector v is an eigenvector,</span>
</p>
  <p>
  <span class="crayon-c"># then c*v is an eigenvector, for any scalar c. (My point is, it’s cool if the signs are switched).</span>
</p>
  <p>
  <span class="crayon-e">plot</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-e">prcomp</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># similar to my plot above with the blue bars</span>
</p>
  <p>
  <span class="crayon-e">summary</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-e">prcomp</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-e">biplot</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-e">prcomp</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">attitude</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-c"># This one is pretty cool…</span>
</p>
  <p>
  <span class="crayon-c"># At the very least, this affirms that what we did above is right! </span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Final Note: Eigenfaces

There's this super cool application of PCA in computer vision called [Eigenfaces](http://laid.delanover.com/explanation-face-recognition-using-eigenfaces/). It basically uses these ideas to do face recognition. It makes me laugh quite a bit to think about the orthogonal components of someone's face hahaha.

[![3](http://i2.wp.com/alexhwoods.com/wp-content/uploads/2016/01/3.png?resize=300%2C201%20300w,%20http://i2.wp.com/alexhwoods.com/wp-content/uploads/2016/01/3.png?w=508%20508w)](http://i2.wp.com/alexhwoods.com/wp-content/uploads/2016/01/3.png)

## _Related_
