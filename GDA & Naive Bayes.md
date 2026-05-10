# Discriminative Vs Generative Algorithms

Learning algorithms can be categorized into 2 kinds: discriminative and generative. The main difference between them is about what's being learned. Discriminative tries to learn a function that gives you the posterior probability

$$
p(y|x;\theta)
$$

While generative algorithms works backwards. That is, it tries to get $p(x|y;\theta)$ and $p(y)$. Then, using the bayesian rule we can get:

$$
p(y|x;\theta) = \frac{p(x|y;\theta)p(y)}{p(x)}
$$

where you can get $p(x)$ by partitioning. So, for example, if $y$ is binary, then

$$
p(x) = p(x|y=0)p(y=0) + p(x|y=1)p(y=1)
$$

In other words, for example, discriminative algorithms like logistic regression tries to directly draw a line that best separate between the 2 different labels of data. A generative algorithm on the other hand makes a decision by considering how the input are distributed with respect to the label and from there make a guess.

There are some reasons why this would be of interest. Particularly, generative algorithms would allow you to provide more assumptions into your systems and hence perform better in some cases. Additionally, at times it would also provide a simpler formula so it's very useful for prototyping and efficient to compute.

# Gaussian Discriminant Analysis

Contradictory to its' name, this algorithm (GDA) is actually a generative algorithm. We start with the following assumptions

$$
p(x|y=0) \sim N(\mu_1, \Sigma)\\
p(x|y=1) \sim N(\mu_2, \Sigma)
$$

Now, $x$ here could be a vector so we will be using the multivariate gaussian defined as follows:

$$
p(x; \mu, \Sigma) = \frac{1}{\sqrt{2\pi}|\Sigma|^{1/2}}e^{-\frac{1}{2}(x-\mu)^T\Sigma^{-1}(x-\mu)}
$$

> $\Sigma$ is assumed to be the same. This is just a choice and if you like you could make them different as well.

We can now talk about how likely our data set is and set up the joint likelihood

$$
\mathscr{L}(\theta) = \prod_ip(x^{(i)} \cap y^{(i)}) = \prod_ip(x^{(i)}|y^{(i)})p(y^{(i)})
$$

What about $p(y)$? The pdf (or pmf) of $p(y)$ depends on what the output is. If it's binary, then you could use a bernoulli. If it has multiple different values, then multinomial.

> I suppose, if $y$ is a real value, then maybe a gaussian could also be used.

You can then get the log-likelihood and derive a rule for $p(y)$ and $p(x|y)$.

An interesting tidbit here is that now we're maximising something called the joint likelihood instead of the conditional likelihood as you do with discriminative algorithms. But how come? Well, I think the idea lies in the fact that now both $x$ and $y$ has probability of happening instead of just $y$ and taking $x$ as a constant and that $y$'s distribution is independent of $x$

## Comparisons With Logistic Regression

The output of GDA is a conditional distribution, $p(x|y)$ and the prior $p(y)$. In the case that $y$ is binary, you'll get both $p(x|y=0), p(x|y=1)$. What you do next is to compute $p(y|x)$ based on these values using the bayesian rule and determine which one's more likely. Particularly

$$
\argmax_yp(y|x) = \argmax_y\frac{p(x|y)p(y)}{p(x)}
$$

Sometimes we just omit $p(x)$.

Graphically, you can imagine that you have two gaussian distributions and as you put in your input $x$, you're seeing which one's $x$ is most likely to be or in this case, which one has a mean that's closest to $x$

![alt text](<sources/GDA & Naive Bayes/Screenshot 2026-01-17 at 12.35.37 AM.png>)

Interestingly, You can also somehow view GDA as producing a boundary line. But as in this figure, the line is not necessarily the same as a discriminative algorithm.

## GDA As A Logistic Regression Variant

Though, GDA and logistic regression both actually uses some kind of sigmoid function. You can show this by evaluating the actual $p(y|x)$ for either labels of $y$. Another way of looking at this is to graph it

![alt text](<sources/GDA & Naive Bayes/Screenshot 2026-01-17 at 12.56.13 PM.png>)

So, GDA is actually Logistic regression + some assumption regarding the input distribution of data. What this means is that the assumptions we use for GDA actually implies the set of assumption used by logistic regression but the assumption of logistic regression i.e. $p(y=1|x)$ is a logistic function, doesn't imply GDA's assumption on the distribution of $x|y$.

## Derivations

$$
L(\phi, \mu, \Sigma) = \log\left(\prod_i \frac{\phi^{y_i}(1-\phi)^{1-y_i}}{2\pi|\Sigma|^{1/2}}e^{-\frac{1}{2}(x_i-\mu)^T\Sigma^{-1}(x_i-\mu)}\right)\\
\,\\
= \sum_i\log(\phi^{y_i}(1-\phi)^{1-y_i}) - \log(2\pi|\Sigma|^{1/2}) - \frac{1}{2}(x_i-\mu)^T\Sigma^{-1}(x_i-\mu)\\
\,\\
= \sum_iy_i\log(\phi) + (1-y_i)\log(1-\phi) - \log(2\pi|\Sigma|^{1/2}) - \frac{1}{2}(x_i-\mu)^T\Sigma^{-1}(x_i-\mu)
$$

For the time being lets think of $\mu = \mu_1^{(y_i)}\mu_2^{(1 - y_i)}$. Now we'll have to split this accordingly. Lets start with $\phi$

$$
\frac{dL(\phi, \mu, \Sigma)}{d\phi} = \sum_i\frac{y_i}{\phi} - \frac{1-y_i}{1-\phi} \implies \sum_i y_i - y_i\phi - \phi + y_i\phi = \sum_i y_i - \phi = 0
$$

Which leaves us with:

$$
\therefore \phi = \frac{1}{N}\sum_i^Ny_i
$$

To get $\Sigma$ we need to be a little more tricky. Essentially, the idea is to take the derivative with each $\Sigma_{i,j} \isin \Sigma$. But, we have an issue: what about the $\Sigma^{-1}$ term? Turns out, if you instead take the derivative w.r.t to the element of the inverse, you still get the same set of conditions.

Before doing that however, we need to establish some identities first. The first one is that

$$
|\Sigma| = \frac{1}{\Sigma^{-1}}
$$

and the second one

$$
x^TAx = tr(x^TAx) = tr(x^TxA)
$$

$tr$ is the trace function and there's a property of it which allows you to basically move things around so long os they're a valid order. Traces have a nice property. Particularly

$$
\frac{\partial}{\partial A}tr(BA) = B^T
$$

because the ith entry of the diagonal can be expressed as $\sum_kb_{i,k}a_{k,i}$. The derivative at $a_{m,n}$ must then happens at $i = n$ and must match the m^th entry at $b_{n,m}$. You can imagine a collection of this would just be $B$ transposed

Why do we need this? Well

$$
\frac{\partial x^TAx}{\partial A} = \frac{\partial tr(x^TAx)}{\partial A} = \frac{\partial tr(x^TxA)}{\partial A} = (xx^T)^T = xx^T
$$

One more:

$$
\frac{d |A|}{dA} = \tilde{A}
$$

This follows from this formula of determinants: $|A| = \sum_i(-1)^{i+j}a_{i,j}M_{i,j}$ for a given column $j$ and $M_{i,j}$ is the determinant of the matrix if you take out the ith row and jth column (also called the cofactor). And, from the definition of inverses

$$
A^{-1} = \frac{1}{|A|}\tilde{A}
$$

Hopefully you see where this is going now. We can rewrite the original equation as

$$
\sum_i \dots - \frac{1}{2}\log(2\pi) + \frac{1}{2}\log(|\Sigma^{-1}|) - \frac{1}{2}tr((x_i-\mu)^T(x_i-\mu)\Sigma^{-1})
$$

Which then if we take the derivative

$$
\frac{\partial L(\phi,\Sigma, \mu)}{\partial \Sigma^{-1}} = \sum_i \frac{1}{2}\Sigma - \frac{1}{2}(x_i-\mu)(x_i-\mu)^T = 0\\
\,\\
N\Sigma = (x_i-\mu)(x_i-\mu)^T\\
\,\\
\therefore \Sigma = \frac{1}{N}\sum_i (x_i-\mu)(x_i-\mu)^T
$$

Finally, we'll be taking derivatives for the $\mu$. Before doing so, we note that

$$
\frac{\partial}{\partial x}x^TAx = (A+A^T)x
$$

Then

$$
\frac{\partial L(\mu, \Sigma, \phi)}{\partial \mu_i} = -2\sum_{j}1\{y^{(j)} = i\}\Sigma(x^{(j)}-\mu_i) = 0\\
\,\\
\implies \mu_i\sum_j1\{y^{(j)} = i\} =  \sum_j1\{y^{(j)} = i\}x^{(j)}\\
\,\\
\therefore \mu_i = \frac{\sum_j1\{y^{(j)}\}x^{(j)}}{\sum_j1\{y^{(j)} = 1\}}
$$

# Spam Filter: Naive Bayes

Say we would like to build an email spam filter. That is given an email, we would like to identify whether it's spam or not spam. The first part that we would be concerned about is with what part of the email are we going to process on i.e. What will be our $x$?

A possible way of doing this is to consider that you have a vector filled with words say from a dictionary. Typically you'll have a limited amount of words (not as much as the words in a dictionary) and usually it would be from old emails that is to be part of your training set.

These word vectors would represent an email and be $x \in \set{0,1}^m$ where the value $1$ would mean the word exist in that email.

Now, a naive way we can do this using a generative algorithm would be to consider $p(x|y)$. Such a setup would lead to the multinomial distribution because $x$ is discrete. With this being the case, we would need $2^n$ possible parameters (for context, $n$ is usually quite big). This is impractical.

We'll have to make some further adjustment. Particularly, we'll be making a little assumption and instead of talking about the random variable $x$, we'll talk about $x_i$ as a random variable. From a rule in probability:

$$
p(x|y) = p(\cap_i x_i|y) = p(x_1|y)p(x_2|x_1 \cap y)p(x_3|x_1\cap x_2 \cap y)\dots p(x_n|(\cap_i^{n-1}x_i) \cap y)
$$

Our assumption will be that

$$
p(x|y) = \prod_ip(x_i|y)
$$

This means that the $x_i$ are conditionally independent and in effect it basically says that in a particular type of email, the probability that $i^{th}$ word is there is independent of any other words in that email.

With this being the case, we can once again set up a maximum likelihood computation, with $y, x_i|y \sim B(\phi)$ and figure out the parameters we need.

# References

1. https://www.youtube.com/watch?v=nt63k3bfXS0&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=5
2. https://people.eecs.berkeley.edu/~jordan/courses/260-spring10/other-readings/chapter13.pdf
