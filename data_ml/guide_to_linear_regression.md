# Guide to Linear Regression

[Original URL](http://alexhwoods.com/guide-to-linear-regression/)

> Linear regression is one of the first things you should try if you're modeling a linear relationship (actually, non-linear relationships too!). It's fairly simple, and...

Linear regression is one of the first things you should try if you're modeling a linear relationship (actually, non-linear relationships too!). It's fairly simple, and probably the first thing to learn when tackling machine learning.

At first, linear regression shows up just as a simple equation for a line. In machine learning, the weights are usually represented by a vector θ (in statistics they're often represented by A and B!).

[![Screen Shot 2015-07-17 at 11.12.35 AM](http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-17-at-11-12-35-am.jpg?resize=300%2C79%20300w,%20http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-17-at-11-12-35-am.jpg?w=363%20363w)](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/screen-shot-2015-07-17-at-11-12-35-am.jpg)

But then we have to account for more than just one input variable. A more general equation for linear regression goes as follows – we multiply each input feature Xi by it's corresponding weight in the weight vector θ. This is also equivalent to theta transpose times input vector X.

[![linreg1.2](http://i2.wp.com/alexhwoods.com/wp-content/uploads/2015/07/linreg1-2.jpg?resize=300%2C94%20300w,%20http://i2.wp.com/alexhwoods.com/wp-content/uploads/2015/07/linreg1-2.jpg?w=386%20386w)](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/linreg1-2.jpg)

There are two main ways to train a linear regression model. You can use the normal equation (in which you set the derivative of the negative log likelihood NLL to 0), or gradient descent.

Sorry for switching notation below. Note – the matrices are _i x j_. _i_ signifies rows, or training examples.

## Gradient Descent

The cost function is essentially the sum of the squared distances. The "distance" is the vertical distance between the predicted y and the observed y. This is known as the residual.

[![Unknown](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/unknown1.png?resize=394%2C262)](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/unknown1.png)

It achieves this by stepping down the cost function to the (hopefully) global minimum.

Here is the cost function –

[![linregCost](http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/linregcost.jpg?resize=300%2C93%20300w,%20http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/linregcost.jpg?w=400%20400w)](http://i1.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/linregcost.jpg)

The cost is the residual sum of squares. The (1/2) is just a constant to make the derivative prettier. You could put 1000 as a multiple of the cost function, it doesn't change the process of minimizing it. Sometimes you'll see m (number of training examples) out front in the denominator too. It would be present in the derivative as well then, because it's a constant. This just makes 'cost per training example', which is perfectly valid.

And the gradient descent algorithm –

[![linregGD](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/linreggd1.jpg?resize=300%2C80%20300w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/linreggd1.jpg?w=471%20471w)](http://i1.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/linreggd1.jpg)

This is really the current weight minus the alpha times the partial derivative of the cost function. Or, in math..

[![latex-image-2](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/latex-image-2.png?resize=300%2C136%20300w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/latex-image-2.png?w=385%20385w)](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/latex-image-2.png)

The original equation switches the position of h(x) and y, to pull out a negative. This makes the equation prettier.

**Learning rate alpha** is a value that determines how big each individual hop down the cost function is. If alpha is too small, gradient descent can take longer than you want to train. If alpha is too big, it can hop over the minima.

**The entire goal of gradient descent is to learn the optimal parameters theta.** These are also referred to as weights.

[Here](http://cs229.stanford.edu/notes/cs229-notes1.pdf) is a cool resource where Andrew Ng breaks down the derivative of the cost function. And [here](http://www.bogotobogo.com/python/python_numpy_batch_gradient_descent_algorithm.php) is a great tutorial on coding gradient descent yourself in Python.

But today, I'm just covering linear regression in R. I believe R is a lot stronger for linear regression, and most things more statistics-like.

## Linear Regression in R

I'm using a sample crime dataset that's loaded into R. You can download it on my [github](https://github.com/alexhwoods/alexhwoods.com/tree/master/Machine%20Learning/Linear%20Regression).

First, let's look at one feature, and throw in a linear model[1].

<span class="crayon-title">Making the plot</span>

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">a</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">ggplot</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">crime</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-e">aes</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">x</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">policeFunding</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">y</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">reportedCrime</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">+</span>
</p>
  <p>
  <span class="crayon-e">geom_smooth</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">method</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">lm</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">colour</span>
  <span class="crayon-o">=</span>
  <span class="crayon-s">“#0BB5FF”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">+</span>
</p>
  <p>
  <span class="crayon-e">ylab</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Crime”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">+</span>
</p>
  <p>
  <span class="crayon-e">xlab</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Police Funding”</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-o">+</span>
</p>
  <p>
  <span class="crayon-e">ggtitle</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-s">“Predicting Crime Rate”</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[![plot1](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/plot1.jpeg?resize=300%2C226%20300w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/plot1.jpeg?w=627%20627w)](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/plot1.jpeg)

The shaded area is a confidence region.

Note – remember correlation doesn't equal causation? Well, this is a good example. More police funding per capita doesn't _cause_ a higher crime rate. It's likely the other way around.

Now let's look at lm() a little more.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">lr</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">lm</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">reportedCrime</span>
  <span class="crayon-o">~</span>
  <span class="crayon-v">policeFunding</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">data</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">crime</span>
  <span class="crayon-sy">)</span>
</p>
  <p>
  <span class="crayon-v">lr</span>
  <span class="crayon-p"># returns the weights (theta).</span>
</p>
</div>
 | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

[![Screen Shot 2015-07-19 at 1.04.34 AM](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-04-34-am.jpg?resize=300%2C72%20300w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-04-34-am.jpg?w=472%20472w)](http://i0.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-04-34-am.jpg)

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">lr</span>
  <span class="crayon-sy">$</span>
  <span class="crayon-v">residuals</span>
  <span class="crayon-p"># residuals</span>
</p>
  <p>
  <span class="crayon-e">plot</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">lr</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-p"># 4 plots to evaluate your model</span>
</p>
  <p>
  <span class="crayon-e">anova</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">lr</span>
  <span class="crayon-sy">)</span>
  <span class="crayon-p"># Analysis of Variance Table</span>
</p>
</div>
 | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Multiple Regression

In this case, we shouldn't limit ourselves to one input feature. We have several that would all make the model better. This is the line to train a simple multiple regression model. All the same functions from before apply.

 | <div class="crayon-pre">
  <p>
  <span class="crayon-v">lr2</span>
  <span class="crayon-o">&lt;</span>
  <span class="crayon-o">-</span>
  <span class="crayon-e">lm</span>
  <span class="crayon-sy">(</span>
  <span class="crayon-v">reportedCrime</span>
  <span class="crayon-o">~</span>
  <span class="crayon-v">graduatedHS25</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">teensHS</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">inCollege</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">graduatedCollege25</span>
  <span class="crayon-o">+</span>
  <span class="crayon-v">policeFunding</span>
  <span class="crayon-sy">,</span>
  <span class="crayon-v">data</span>
  <span class="crayon-o">=</span>
  <span class="crayon-v">crime</span>
  <span class="crayon-sy">)</span>
</p>
</div>
 | -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Here are a few of the features visualized[2] –

[![Screen Shot 2015-07-19 at 1.12.12 AM](http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-12-12-am.jpg?resize=300%2C225%20300w,%20http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-12-12-am.jpg?resize=768%2C575%20768w,%20http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-12-12-am.jpg?resize=1024%2C767%201024w,%20http://i0.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-12-12-am.jpg?w=1106%201106w)](http://i2.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-12-12-am.jpg)

Or if we want to see the residuals...[](http://74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-12-37-23-am.jpg)

[![Screen Shot 2015-07-19 at 1.32.12 AM](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-32-12-am.jpg?resize=300%2C226%20300w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-32-12-am.jpg?resize=768%2C579%20768w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-32-12-am.jpg?resize=1024%2C772%201024w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-32-12-am.jpg?w=1106%201106w)](http://i0.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/screen-shot-2015-07-19-at-1-32-12-am.jpg)

## 

## 

## 

## A few more notes about linear regression –

It can map non-linear relationships[3], if you replace the input vector x with a non-linear function Φ(x). This is useful, but you have to figure out whether you want Φ(x) to be x-squared, or some other exponential, quadratic, logarithmic form...I would try another algorithm (perhaps an SVM!).

This last part is a bit random, I just happened to love the math here.

You can represent linear regression in it's relationship to a Gaussian distribution. If you replace predicted y (yhat) with observed y, you have to add an error 

<span id="MathJax-Element-7-Frame" class="MathJax">
  <span id="MathJax-Span-17" class="math">
  <span id="MathJax-Span-18" class="mrow">
  <span id="MathJax-Span-19" class="mi">ϵ. </span>
</span>
</span>
</span>

[![linreg2](http://i0.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/linreg2.jpg?resize=293%2C65)](http://i0.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/linreg2.jpg)

However, ϵ is often assumed (probably correctly) to have a normal distribution[4]. So we can rewrite –

[![latex-image-1](http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/latex-image-1.png?resize=300%2C120%20300w,%20http://i1.wp.com/alexhwoods.com/wp-content/uploads/2015/07/latex-image-1.png?w=322%20322w)](http://i0.wp.com/74.220.219.134/~alexhwoo/wp-content/uploads/2015/07/latex-image-1.png)

This gives us a different way of thinking about y. It's now the output of a normal distribution whose mean is changing. I really like this representation!

Sources –

1. Winston Chang's [Cookbook for R](http://www.cookbook-r.com/Graphs/Scatterplots_(ggplot2)/).

2. Scatterplot3d [documentation](https://cran.r-project.org/web/packages/scatterplot3d/vignettes/s3d.pdf).

3. Kevin Murphy's super awesome [textbook](http://www.cs.ubc.ca/~murphyk/MLbook/).

4. Some [lecture notes](http://www.cc.gatech.edu/~lebanon/notes/linReg.pdf) from Georgia Tech.

### _Related_

**Tags:** [important](http://alexhwoods.com/tag/important/), [R](http://alexhwoods.com/tag/r/)
