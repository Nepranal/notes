# K-Clustering Algorithm

This will be out first introduction to unsupervised learning algorithms. Informally, it's a study of how we can fit learning algorithms withough using labels but with the patterns within the data itself.

The K-Clustering algorithm is one instance of such. Given a particular set of data, we would like to make 2 different groups within. How can we do so?

![alt text](<sources/Expectation-Maximization Algorithms/Screenshot 2026-05-12 at 12.31.25 AM.png>)

The K-Clustering algorithm will get a distance, for each point, to each of the location of the crosses; the group's centre. The data point will be labelled to whatever is closest group(w.r.t the euclidian distance). Once this is done, you'll recalculate the group's average and redo the same operation again. Repeat until convergence. In general, you can set up $K$ groups.

It can be shown that the clustering algorithm will converge as well. The rationale behind it is, there is an objective function

$$
J(c,\mu) = \sum_i|x^{(i)} - \mu_{c^{(i)}}|^2
$$

where $\mu_{c^{(i)}}$ is the centroid it has been assigned to. It can be shown that the clustering algorithm will bring this cost function down every iteration

# Density Estimation

Say we're posed with the following graph

![alt text](<sources/Expectation-Maximization Algorithms/Screenshot 2026-05-12 at 1.26.23 AM.png>)

How could we statistically determine the outlier (green dot)? An idea is to model the data we have using some sort of probability distribution $p$. We then consider the anomalous points by considering their likelihood i.e. if $p(x)<\epsilon$, then we consider it an anomaly

The idea here is to view that an anomaly can be seen as a point that's far away from other trends of data. With the example given above, we can somewhat see that there are 2 groups and the green dot is reasonably far from either one. For this particular one, we'll consider the groups to come from a gaussian distribution.

Let's consider a 1D example

![alt text](<sources/Expectation-Maximization Algorithms/Screenshot 2026-05-12 at 1.41.41 AM.png>)

If we know, that each data point comes from a separate gaussian and which belong to who, then we can get the parameter of each gaussian and be done with our task. The approach we're going to take will actually be about guessing the "who's belong to what" part

In order to do this systematic guessing, we'll introduce the concept of latent variables. Latent simply means hidden / unobserved

Assume there exist a latent variable $z$. $z$ can take multiple discrete values. The distribution between $x$ and $z$ can be described as

$$
p(x, z = j) = p(x|z = j)p(z = j)
$$

The $z$ here represents from which of the number of gaussian we have decided that originates our data. Hence we'll say that $x|z = j \sim N(\mu_j, \Sigma_j)$ and $p(z_j) = \phi_j$.

This is actually somewhat similar to Gaussian Discriminant Analysis. In fact if we guess the data comes from 2 gaussian, we'll have exactly that except we don't have the data labels else we could've solved for our parameters ($\mu_j, \Sigma_j$).

Here we'll use something called Expectation-Maximisation (EM) algorithm. The algorithm is split into 2 steps: The E-step and M-step. The E-step tries to guess, for each data point, the probability that it belongs to gaussian $j$

$$
w_j = p\left(z^{(i)} = j | x^{(i)}; \mu_j, \Sigma_j\right) = \frac{p\left(x^{(i)} | z^{(i)} = j; \mu_i, \Sigma_j\right)p(z^{(i)} = j)}{\sum_j^kp(x^{(i)}|z_j^{(i)})}
$$

You can get this expression by considering baye's rule.

> Weirdly enough, the lecture never said anything about the initial values of the quantities. I read somewhere that you can just randomly intialize them in which case you have 2 choices. Either intialize the parameter or intialize the $w_j$ directly...

The M-step is to recalculate the parameters again. There's a set of equations for them which I won't write here as I've no clue where they come from but you get the idea that it's an iterative algorithm

Once you're satsifified, you basically now have a joint distribution between $x$ and $z$. To get the probability of $x$ then

$$
p(x) = \sum_j^kp(x|z = j)
$$

and if $p(x)$ is smaller than some treshold $\epsilon$, we'll label it as an anomaly

# Expectation Maximization Algorithm

Let's delve further and try to generalize this algorithm. Given some dataset our goal is to maximise the likelihood (or log likelihood)

$$
\max_\theta \prod_ip(x^{(i)};\theta) = \max_\theta\sum_i\log(p(x^{(i)};\theta))
$$

The method then tries to sublet a latent variable into the mixture converting the equation into

$$
\sum_i\log\left(\sum_j p(x^{(i)},z^{(i)}_j)\right)
$$

Here's something we can do: we can multiply and divide that internal term with a dsitribution $Q_i$ defined per sample and is a distribution of $z$ i.e. $\sum_jQ_i(z_j) = 1$

$$
\sum_i\log\left(\sum_jQ_i(z^{(i)}_j)\left[\frac{p(x^{(i)},z^{(i)}_j)}{Q_i(z^{(i)}_j)}\right]\right) = \sum_i\log(E_{z^{(i)} \sim Q_i}[z_j])
$$

Here, we can actually see the inner term to be an expected value. Imagine that $Q_i(z_j)$ is the probability of $z_j$ being the value inside the bracket

> this is why we define a $Q$ per sample I think

The purpose of this whole setup is to use jensen's inequality which says that for a convex function $f$, we can say

$$
f(E[X]) \geq E[f(X)]
$$

for a given random variable $X$. We can see that the original maximisation problem to be a function of $\theta$ which then you can reason to be a logarithmic function which is convex. The bracket term, the term $z^{(i)}_j$ could take the values of is also a function of $\theta$. We can then say

$$
\sum_i\log(E_{z^{(i)} \sim Q_i}) \geq \sum_iE\left[\log\left(\frac{p(x^{(i)}, z_j^{(i)})}{Q_i(z_j^{(i)})}\right)\right]
$$

The term on the right can be written as

$$
\sum_i\sum_jQ(z^{(i)}_j)\log\left(\frac{p(x^{(i)}, z_j^{(i)})}{Q_i(z_j^{(i)})}\right)
$$

Why do we want to do all this? Essentially, what we're trying to do is to achieve the following

![alt text](<sources/Expectation-Maximization Algorithms/Screenshot 2026-05-14 at 11.44.18 PM.png>)

At each point, we find an approximation of our original objective as a lower bound. Then we maximize that approximation (in the hopes that it's easier to maximise) which will also hopefully corresponds to maximising our original objective.

In order for us to have some sort of certainty that maximising the approximation will correspond to maximising the objective, as the graph shows, we need to have it be tangential to it - a point of intersection.

There's an amendment to Jensen's inequality that we can use. That is, the inequality will become an equality if $X$ is a constant. What this means for us is that we have to make sure the bracketed term is a constant. We can do this by defining $Q_i$ to be

$$
Q_i(z^{(i)}) = p(z^{(i)} | x^{(i)})
$$

In effect will turn our inequality at point $\theta$ to be equivalent.

> $Q_i(z^{(i)})$ is a function of $\theta$

So then the EM algorithm shows itself:

First we initialize some values of $\theta$, and then

1. Set $Q_i = p(x^{(i)}|z^{(i)})$
2. solve $\max_\theta \sum_i\sum_jQ(z^{(i)}_j)\log\left(\frac{p(x^{(i)}, z_j^{(i)})}{Q_i(z_j^{(i)})}\right)$ which will give a new set of $\theta$
3. Set old $\theta$ to be new $\theta$
4. Repeat until convergence/satisfied

# References

1. https://www.youtube.com/watch?v=rVfZHWTwXSA&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=14
