# Processes of Learning Algorithms

We make the following assumptions:

1. There exist a distribution $D$ where $(x^{(i)}, y^{(i)}) \sim D$
2. Independent samples

You can the imagine the process of learning algorithms would be to first input a some data samples, sampled from $D$, into a learning algorithm and getting back a hypothesis $\hat{h}$. $\hat{h}$ is a random variable with a distribution w.r.t the sample size. We call this distribution of $\hat{h}$ the sampling distribution

By distribution here I do mean that for a given sample size $m$, there's a probability that you'll get algorithm $\hat{h}$.

There is an optimal $h^*$ that we would like to get which we call the 'true' paramaters. These true parameters are constants; they don't have an associated distribution and so is not a random variable

Note: I take that $\hat{h}$ is basically the same thing as $\hat{\theta}$ while writing this note

# Parameter's View of Bias and Variance

Imagine we have 4 algorithms: A, B, C and D. We take a fixed sized training dataset $S$ from $D$ and train on 4 of these algorithms. Then, we repeat the process by resampling $S$ from $D$. Then, if you sample the $\hat{h}$ you got, what you could end up seeing are these plots

![alt text](<sources/Learning Theory/Screenshot 2026-04-07 at 1.00.26 PM.png>)

(given you know what $h^*$ is) where the star indicates the true parameters.

These 4 plots demonstrate what bias and variance are. Bias describes how centered you are to the true parameters while variance tells how disperse your learning algorithm is.

> If $E[\hat{h}] = h^*$ i.e. keeps centering around the true parameter, for all sizes of $S$, we call your learning algorithm an 'unbiased estimator'

## Increasing Number Of Samples

Due to statistics, as the number of samples, $m$, gets big, the variance of your algorithm reduces.

If your algorithm reached the true parameter as $m$ gets large we call your algorithm 'consistent'.

# Adding Regularization

Regularization allows us to reduce the variance of our algorithms by restricting the type of algorithms we can get but at the same time increases bias.

With our interpretation, this would mean that the spread of your algorithm given a sample size would decreases but at the same time your bias may increase as well.

# Hypothesis Space

This is how we generally view the relationship between $\hat{h}$ and $h^*$

![alt text](<sources/Learning Theory/Screenshot 2026-04-07 at 1.39.14 PM.png>)

Here we add another algorithm $g$. Both $\hat{h}, h^*$ exist in a class called $H$. Typically $H$ contains algorithms of the same type. Say in this case $H$ could be the class for all logistic regression algorithms

$g$ is the 'best' algorithm. 'Best' here means the one with the lowest error. $g$ may or may not be in $H$.

One more thing to note, these are defined w.r.t to a particular data $D$. And so, we define a couple of measures

- $\epsilon(h) = E_{(x,y) \isin D}[1\{h(x) \neq y\}]$ - Generalization error
- $\hat{\epsilon}(h) = \frac{1}{m}\sum_i1\{h(x^{(i)}) \neq y\}$ - Emperical error

Both are practically the same thing just defined at different sets

# Measuring $\epsilon(\hat{h})$

We split it into the following

$$
\epsilon(\hat{h}) = (\epsilon(\hat{h}) - \epsilon(h^*)) + (\epsilon(h^*) - \epsilon(g)) + \epsilon(g) = \xi + \alpha + i
$$

- $\epsilon(\hat{h}) - \epsilon(h^*) = \xi$ is what we call the approximation error. This is the error difference between the best and your current estimate
- $\epsilon(h^*) - \epsilon(g) = \alpha$ is the approximation error. This error comes up as you choose the type of algorithms you want e.g. Logistic regression, neural network, etc.
- $\epsilon(g) = i$ is the minimum error you will pay. It's also called the irreducible error

The error decomposition can be broken down some more. The estimation error can be broken down to estmation bias and estimation variance. Lets label those as $\beta$ and $\sigma$. This leads to

$$
\epsilon = \sigma + \beta + \alpha + i
$$

$\alpha + \sigma$ is what we refer to as the bias. Here we see that bias plays the role of either your class is too small or your algorithm is unfit. We would like to have $\hat{h}$ to be close to $g$.

# Emperical Risk Minimizer

The Emperical Risk Minimizer (ERM) is a learning algorithm that simply minimizes the emperical error. Some would say it's the simplest algorithm

$$
\argmin_h\frac{1}{m}\sum_i1\{h(x^{(i)}) \neq y^{(i)}\}
$$

Algorithms like logistic regression actually minimizes something similar to ERM (by means of gradient descent). But, the reason for ERM is that we can draw some more stuff out of it as we will see

# Uniform Convergence

We are interested in two questions

1. $\epsilon(h)$ vs $\hat{\epsilon}(h)$ - What does the emperical error of $h$ says about it's generalization error
2. $\epsilon(h)$ vs $\epsilon(h^*)$ How does the generalization error of the estimate compares with the optimal in the class?

To explore these questions, we'll make use of a couple of tools

## Union Bound

For events $A_1, A_2,\dots,A_k$ for some $k \isin \Z \geq 1$

$$
P\left(\bigcup_iA_i\right) \leq \sum_iP(A_i)
$$

This is based on probability. The equal case is when the events $A_i$ are independent or if $k = 1$ (you get the idea)

## Hoeffding's Inequality

Say we have $m$ samples

$$
Z_1,Z_2,\dots,Z_m \sim B(\phi)
$$

and that

$$
\hat{\phi} = \frac{1}{m}\sum_i Z_i
$$

Then we can say

$$
P(|\phi^* - \hat{\phi}|>\gamma) \leq 2\exp(-2\gamma^2m)
$$

for which $\gamma>0$. This theorem basically says that for the given estimator, we can give an upper bound on it's error w.r.t the optimal answer given it's bigger than a margin $\gamma$. Or in other words, there's like a confidence interval thing we can give

# $\epsilon(h)$ vs $\hat{\epsilon}(h)$

We can imagine the following

![alt text](<sources/Learning Theory/Screenshot 2026-04-07 at 4.34.35 PM.png>)

we drew for each possible hypothesis their generalization error and their emperical error.

Let's consider for a particular $h_i$ the quantity $\epsilon(h) - \hat{\epsilon}(h)$. One thing that we should know is that $E(\hat{\epsilon}(h)) = \epsilon(h)$ that is on average, the emperical error is the generalization error

> I'm actually not sure where this fact comes from

According to hoeffding inequality

$$
P(|\epsilon(h_i) - \hat{\epsilon}(h_i)| > \gamma) \leq 2\exp(-2\gamma^2m)
$$

Then as $m \rightarrow \infin$, we can reasonably show that the dotted line becomes closer and closer to the generalization error given that the estimator is simply taking averages.

Though we want to show that this is true in general for all $h$ given a large dataset. We'll then consider the inequality for all possible $h$ and see how the emperical error converges to the generalization error (hence uniform convergence). To consider all $h$ however comes in 2 different cases: discrete and continuous. We'll only consider the former here

By using the union bound, we can get

$$
P(\exist h \isin H |\epsilon(h) - \hat{\epsilon}(h)|>\gamma) \leq k2e^{-2\gamma^2m}
$$

Which basically says that if you minimize the emperical error, you'll also minimize the generalization error

# Continuous Case

# References

1. https://www.youtube.com/watch?v=iVOxMcumR4A&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=9&t=601s
