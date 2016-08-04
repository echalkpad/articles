# Factorization Machines

[Original URL](http://tech.adroll.com/blog/data-science/2015/08/25/factorization-machines.html)

> The data science team at AdRoll is constantly working to improve our programmatic bidding algorithm, BidIQ. One recent improvement to BidIQ has been the introduction of a novel modeling technique...

The data science team at AdRoll is constantly working to improve our programmatic bidding algorithm, BidIQ. One recent improvement to BidIQ has been the introduction of a novel modeling technique called Factorization Machines (FM). The FM model allows us to consider interactions between all predictive variables, rather than only certain manually selected interactions. Thanks to the FM model, we are able to use the exact same feature information to more accurately value every potential impression that comes our way. As a result, our advertisers are collecting almost 7% more clicks for only 2% more cost.

To motivate the FM model, let's consider a simplified ad bidding algorithm that bids using three variables: web domain, advertiser, and user location. In the first iteration of our modeling, we would learn a weight for each web domain, advertiser, and user location. Our bid, , would then be based on a function of a linear combination of the weight vector . That is:

where is an intercept term and is the value of the th feature. This modeling technique proved extremely powerful, but the drawback was that the model only learned the effect of our three variables individually rather than in combination. But what if an advertiser's ads perform particularly well on some specific domains? What if users in San Francisco are much more interested in some advertisers than others? What if a particular domain tends to receive exceptionally valuable traffic from New York? One popular way of solving this problem at scale is to supplement the standard linear model equation with an additional term to model pairwise feature interactions.

The simplest such strategy is to learn a weight for each feature combination. We would then make our bid, , according to:

where are additional parameters to be learned. Unfortunately, this naïve approach will not work for two main reasons. First, the size of the model is now , which has terrible implications for both the amount of memory needed to store the model, and the time it takes to train the model. Second, our dataset is too sparse for us to learn the all of the weights reliably. That is, for almost all pairs we would not have enough training examples to learn the weight well. To improve the pairwise feature interaction modeling of , we could include only the weights with corresponding features sufficiently dense and informative. In the second iteration of our modeling, we did just this. The problem is selecting the valuable weights is difficult to do algorithmically, and does not scale to a large number of possible feature combinations.

FM solves the problem of considering pairwise feature interactions. Indeed, it allows us to bid based on reliable information from every pairwise combination of variables in the model. Just as important, FM allows us to do this in a remarkably efficient way both in terms of both time and space complexity. So how exactly does FM work? FM models pairwise feature interactions as the inner product of low dimensional vectors. More precisely, our bid with the FM model, , becomes:

where are additional parameters to be learned, and is the th row of . Notice that the FM model replaces the weights by . From a modeling perspective, this is powerful because each feature ends up embedded in an inner product space, with similar features embedded near one another. As a result, the FM model is even able to learn the effect of interactions between features which appear together very infrequently in the training data. Furthermore, the size of the FM model is now a more reasonable , where the latent dimension is a hyperparameter of the FM model.

However, the FM model is not an obvious improvement from a computation time perspective. In fact, computation of the pairwise feature interaction term now _appears_ to require operations rather than the of the naïve interaction modeling solution. Yet this is not the case; after some manipulation we may rewrite the nonlinear FM term as follows 

<sup>
  <a href="http://tech.adroll.com/blog/data-science/2015/08/25/factorization-machines.html#footnotes">(1)</a>
</sup>

:

The right hand side of this equation can clearly be computed in time. This is the magic of FM: _we are able to compute the term that models all pairwise interactions in linear time._ As a result, we are able to train the FM model in a time proportional to the time needed to train the linear model.

Apart from the optimization described above, we used several other strategies to reduce training time and improve convergence. First, the FM model is trained using stochastic gradient descent (SGD), an algorithm known for its speed. 

<sup>
  <a href="http://tech.adroll.com/blog/data-science/2015/08/25/factorization-machines.html#footnotes">(2)</a>
</sup>

 Second, we parallelized SGD using the lock-free so-called "HOGWILD!" scheme [[2]](http://tech.adroll.com/blog/data-science/2015/08/25/factorization-machines.html#references). This lock-free, parallel SGD is notable because it avoids blocking any threads by allowing race conditions during model updates. Additionally, we found AdaGrad to be a very effective learning-rate schedule for training an FM model with SGD [[3]](http://tech.adroll.com/blog/data-science/2015/08/25/factorization-machines.html#references). Finally, we used single instruction multiple data (SIMD) computation to vectorize calculations involving the matrix , quartering FM model training time. See below for a D programming language (pseudocode) example of the vectorized calculation of the FM model equation. With the optimizations described we are able to train an FM model in approximately the same amount of time it took to train our previous model. This is outstanding as the FM model is significantly more predictive.

```
class FMModel
{
 uint k; // FM latent dimension
 float w0;
 float[] w_vector;
 float4[][] v_matrix;

 // ... other FM model variables and functions ...

 float model_equation(Observation obs)
 {
 float linear_term = w0;
 foreach(Feature feat; obs)
 linear_term += w_vector[feat.id];

 float4 non_linear_term4 = 0;
 for(int j = 0; j < (k/4); j++)
 {
 float4 first_term4 = 0.toFloat4();
 float4 second_term4 = 0.toFloat4();
 foreach(Feature feat; obs)
 {
 float4 update = v_matrix[j][feat.id] * feat.weight;
 first_term4 += update;
 second_term4 += (update * update);
 }
 first_term4 *= first_term4;

 non_linear_term4 += (first_term4 - second_term4);
 }

 float non_linear_term = non_linear_term4.sum_float4();
 return 0.5 * (linear_term + non_linear_term);
 }
}
```

Moving forward, we continue to search for modeling and model training improvements. If Factorization Machines or machine learning are the types of things that interest you, please consider applying to [work at AdRoll](https://www.adroll.com/about/careers)!

## []() References

1. Rendle, Steffen. _Factorization Machines._
2. Recht, Benjamin and Re, Christopher and Wright, Stephen and Niu, Feng. _Hogwild: A Lock-Free Approach to Parallelizing Stochastic Gradient Descent._
3. Duchi, John and Hazan, Elad and Singer, Yoram. _Adaptive Subgradient Methods for Online Learning and Stochastic Optimization._

<!--  -->

1. For details, please see [[1]](http://tech.adroll.com/blog/data-science/2015/08/25/factorization-machines.html#references).
2. The main downside to using FM is the resulting optimization problem is no longer convex. As a result, many effective optimization techniques are no longer at our disposal when learning the parameters and of the FM model. Fortunately, SGD still works quite well in this non-convex setting.
