# Laplace Smoothing

It's basically the idea to sort of giving padding when you're predicting using past data. Imagine that you are observing a team playing some games and for the past 5 games the teams have lost

**Game**

- game 1: L
- game 2: L
- game 3: L
- game 4: L
- game 5: ?

To predict game 5, a naive approach we can take is to say that $p = \frac{0}{0+4} = 0$. In a way, you're right. But, at the same time, having exactly 0 would lead you to believe that the team will never win. Unless you're sure, a better way to use past data, is to instead say that the chances of winning is extremely small (non zero).

Laplace smoothing allows you to have that kind of notion. We do this by adding an extra sample for each possible category within our data. The calculation then becomes

$$
p(x=1) = \frac{0 + 1}{0 + 1 + 4 + 1} = \frac{1}{6}
$$

We added 1 to both $x=1, x=0$.

In general, if you have $k$ possible outcomes and $m$ data points

$$
p(x=j) = \frac{1 + \sum \delta(x-j)}{m + k}
$$

# Converting To Discrete Features

Let's come back to house value prediction that we did using linear regression. Say we would like to predict whether a house will be sold within 30 days and we would like to use a generative model. An issue is that some of the features, like the house price is not binary valued. How can we incorporate the methods we have studied before like the naive bayes?

To discretize a continuous feature, we can construct buckets where each bucket represents a range. We'll label these buckets (1,2,...,3) and set $x_j$ according to the bucket label. Since the value can now be of multiple categories, when we do naive bayes per se, the distribution is no longer a bernoulli but should now be a multinomial distribution.

# Spam Classifier: Multinomial Event Model

There's something lacking in the email spam classifer using naive bayes: we don't take into account if there are multiples of the same word and the length of the email.

To take account of those scenarios, we'll change our model. Instead of having a vector $x$ of fixed length containing a dictionary, we will now have instead $x \in \R^n$ where $n$ is the length of the email. $x$ contains a list of words placed in that email.

The goal is still the same: compute $p(x|y)$. The assumption also stays the same; $p(x|y) = \prod p(x_j|y)$ but now $x_j$ can have $m$ possible states where $m$ is the number of words in our dictionary. In other words, just like before, $x_j|y$ is now a multinomial distribution and we're considering the probability of having a particular word in our set at the $j$th positition. We can then compute

$$
\prod_i^n p(x_i, y_i) = \prod_i^n p(x_i|y_i)p(y_i)
$$

where $n$ is the number of words in the email.

# Support Vector Machine

The Support Vector Machine is our first exposure to a non-linear learning algorithm. Consider the following

![alt text](<sources/Support Vector Machines/Screenshot 2026-02-24 at 2.50.40 AM.png>)

A linear regression or GDA can only draw a straight line in such a situation. How can make better prediction then? The linear requirement will still be there but we can rectify the situation a bit. To make such a dataset linear, we can add extra features.

So instead of only having $x_1,x_2$, we can have

$$
\phi(x) = \begin{bmatrix}
x_1 \\
x_2 \\
x_1 x_2 \\
x_1^2x_2 \\
\vdots
\end{bmatrix}
$$

$\phi$ is a mapping to a higher dimensional feature vector. Doing so might allow us to have a linear relationship between our plot.

# Optimal Margin Classifier

In the separable case, the svm tries to find a line that optimises the distance between one type and another.

We will first define the functional margin. It defines how confident you are with your classification. Say we're doing linear regression. If the classification is true, then we hope that $g(h_\theta(x)) = 1$ where $h_\theta(x)>>0$ and similarly if $g(h_\theta(x)) = 0$. If we're doing GDA, then we hope that our classification is very close to the mean of one of the gaussians. We would like some way to better quantify those expressions

## Notation Break

- Labels would now be $y \in {1, -1}$; 1 if true, -1 otherwise.
- We have a function $g(x) \mapsto {1,-1}$ where if $x>0$, it will go to $1$.
- Our hypothesis would be $h_{w,b} = g(w^Tx + b)$
  - This is pretty similar to what we have before except now we don't append an extra $1$ to our feature vector.

# Functional Margin

The functional margin is defined per sample

$$
\hat{\gamma}^{(i)} = y^{(i)}(w^Tx^{(i)} + b^{(i)})
$$

The goal is to have $\gamma$ to be as big as possible. This would entail that if $y=1$, we hope that $w^Tx^{(i)} + b^{(i)} >>0$ and similarly if $y=-1$. With this being so, we take the functional margin to be the worst case scenario

$$
\hat{\gamma} = \min_i\hat{\gamma}^{(i)}
$$

## Geometric Margin

The idea is kind of similar to the functional margin: we just want to measure how far the sample is from the line. Except this time we define so with the euclidian distance

$$
\gamma = \frac{y(w^Tx + b)}{||w||}
$$

and so

$$
\gamma = \frac{\hat{\gamma}}{||w||}
$$

In a way, we would like for the the distance to be as far as possible i.e. to be as confident as we can.

These two different margin definitions are after the same goal just with a slightly different interpretation. In fact, as shown, the functional margin can inhibit a similar form with the geometric margin; both parameters being normalized by $||w||$.

# References

1. https://www.youtube.com/watch?v=lDwow4aOrtg&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=6&t=2s
