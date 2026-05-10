# Perceptron

It works similarly and has the same update rules a logistic regression

$$
\theta_j = \theta_j + \alpha x_j^{(i)}(y^{(i)}-h(\theta^Tx^{(i)}))
$$

with

$$
h(z) = \begin{cases}
      1 & z \geq 0 \\
      0 & z < 0
   \end{cases}
$$

where $z = \theta^Tx$.

The main idea of the perceptron is that you're trying to draw a line that can sufficiently classify all the samples by rotating that line / hyperplane around. In the case of perceptrons, the line would be linear instead of affine.

Particularly, the perceptron update rule makes it so that if the data point has $y=1$, then $\theta$ is hopefully similar to $x$ (in terms of direction) and otherwise if $y=0$. Note that $\theta$ is the vector that is orthogonal to the hyperplane.

But why? Well, it's because of the hypothesis function that we have, $\theta^Tx$, is essentially a dot product which is a measure of how similar two vectors are. Our rule then reads the previous statement made.

> However, the method does not actually guarantee this as to be shown.

Now, consider that you already have a line that can perfectly fit your data point and we have a new sample.

![alt text](<sources/Perceptron & Generalized Linear Model/Screenshot 2026-01-16 at 10.30.58 AM.png>)

If this sample is wrongly classified, the algorithm will try to make the angle between the hyperplane's normal vector, $\theta$, smaller by adding some small fraction of the data point into the plane's normal vector. The hope is that you will manage to rotate the plane just right to include that new sample while keeping the others correctly classified.

We can show this fact more mathematically:

Consider vectors $a,b$ where $|a| = |b| = 1$. We know that

$$
a\cdot b = \cos(\theta)
$$

If we then have a vector $a' = a + \alpha b$, then we have to show that

$$\frac{a'}{|a'|}\cdot b - a\cdot b \geq 0$$

Expanding the equation, you can get that

$$
\frac{a'}{|a'|}\cdot b - a\cdot b = \frac{\cos(\theta)(1-|a'|) + \alpha}{|a'|}
$$

Which means we just have to show that

$$
\cos(\theta)(1-|a'|) + \alpha \geq 0 \tag{1}
$$

To do this, geometrically, you can imagine that $a, \alpha b, a'$ are sides of a triangle.

![alt text](<sources/Perceptron & Generalized Linear Model/IMG_A05F2D8A2785-1.jpeg>)

Since $|a| = 1, |\alpha b| = \alpha$, we can say that (you can check Euclid's Element book 1 proposition 20 and I believe this holds true in general for $\R^n$)

$$
\alpha + 1 \geq |a'| \tag{2}
$$

$$
\alpha + |a'| \geq 1 \tag{3}
$$

In $(1)$, the term $\cos(\theta)(1-|a'|)$ has 2 extremes: $1-|a'|, |a'| - 1$. Which one's the max and which one's the min depends on $|a'|$ but regardless those will be the exteme values.

From $(2), (3)$ however, we know that $\alpha$ is bigger than any of these extremes. Hence, the sum must also be a non-negative.

With this being the case, $(1) \geq 0$ and hence
$$\frac{a'}{|a'|}\cdot b - a\cdot b \geq 0$$

$\square$

So you can imagine that if we have a handful of training data, then we'll consider only one sample per update and update the model until it is correctly classified before moving on. You can't however consider a bunch of the training samples at once as it won't guarantee to be able to close the angle to each of those sample points definitely.

## Flaws Of The Perceptron

The Perceptron however is flawed. Take the update rule for instance. Although yes, you can indeed update the model so that it will correctly classify that one particular data sample, it however doesn't guarantee you won't overstep and missclasify some other data. This would mean it would only take one outlier to mess everything up.

Additionally, in the event that the data are not separable linearly, the perceptron doesn't converge to anything. Consider

![alt text](<sources/Perceptron & Generalized Linear Model/Screenshot 2026-01-18 at 11.45.48 AM.png>)

This example is also a good one to consider on why we don't take the sum. In this case, you can't pick anything reasonable for your model. Additionally, it lacks interpretability overall unlike the logistic regression.

# Exponential Family

The exponential family has the following format

$$
p(y;\eta) = b(y)e^{\eta^TT(y)-a(\eta)}
$$

If your distribution can be transformed into the following form, then your distribution is part of the exponential family.

The rule is as follows, $y$ is the input to your distribution while $b,a,T$ are pure functions e.g. if they take in $y$ as a paremeter, then they must not include any other variable. $\eta$ is what's called a natural parameter. You choose $\eta$ once while you try to see if your distribution is part of the exponential family.

> I imagine that $\eta$ is similar to the mean and variance of some distribution i.e. It defines some feature of the distribution.

## Bernoulli Distribution As An Exponential Family

The bernoulli distribution has the following pdf

$$
p(y;\phi) = \phi^y(1-\phi)^{1-y}
$$

Where $\phi$ is the probability of something happening and is the paremeter of this distribution. To massage this into the format:

$$
p(y;\phi) = e^{\ln(\phi^y(1-\phi)^{1-y})} = e^{\ln(\phi^y) + \ln((1-\phi)^{1-y})} = e^{y\ln(\phi) + (1-y)\ln(1-\phi)}
$$

After some rewriting

$$
p(y;\phi) = e^{\ln(\frac{\phi}{1-\phi})y - \ln(\frac{1}{1-\phi})}
$$

Here, we see that $b(y) = 1$, $\eta^T = \eta = \ln(\frac{\phi}{1-\phi})$, $T(y) = y$. For $a(\eta)$, we need to first find $\phi$ in terms of $\eta$.

$$
\phi = \frac{e^{\eta}}{1+e^{\eta}} = \frac{1}{1+e^{-\eta}}
$$

Then we can say that

$$
a(\eta) = -\ln(1-\frac{1}{1+e^{-\eta}})
$$

## Gaussian (constant $\sigma$) As An Exponential Family

$$
p(y;\mu, \sigma) = \frac{1}{\sigma\sqrt{2\pi}}e^{-\frac{(y-\mu)^2}{2\sigma^2}}
$$

$$
=\frac{e^{-\frac{y^2}{2\sigma^2}}}{\sigma\sqrt{2\pi}}e^{\frac{\mu y}{\sigma^2}-\frac{\mu^2}{2\sigma^2}}
$$

Just like before, $\eta = \mu$, $T(y)=y$, $a(\eta) = \eta^2/2$ and that

$$
b(y) = \frac{e^{-\frac{y^2}{2\sigma^2}}}{\sigma\sqrt{2\pi}}
$$

> I'm guessing if $\sigma$ is an unknown, then
>
> $$
>     \eta = \begin{bmatrix}
>           \mu \\
>           \frac{1}{2\sigma^2}
>         \end{bmatrix}
> $$

## Properties Of The Exponential Family

We worked hard so far, so why do we bother with exponential families? It's because of the following properties

1. Maximum Likelihood Estimation (MLE) w.r.t. $\eta$ is a convex problem i.e. You'll always find an extreme value
2. $E[y;\eta] = \frac{d}{d\eta}a(\eta)$
3. $Var[y;\eta] = \frac{dE[y;\eta]}{d\eta}$

> It's actually $E[T(y);\eta]$ (and so is wherever you see $y$ near this point). But because $E(T(y))$ is usually $y$, it's also usually written that way

# Generalized Linear Models

The generalized linear model is a particular subset of the exponential family. We will be making assumptions / design decisions.

- $y|x;\theta$ is an exponential family
- Prediction made would be $E[y|x;\theta]$ i.e. $h_\theta(x) = E[y|x;\theta]$
- $\eta = \theta^Tx$ - This is a design decision

The framework of using the Generalized Linear Model is to simply consider the possible distribution of your data. Say, if your data is true/false, then you may want to use the bernoulli distribution. From there you will get the hypothesis (2nd property of exponential family and 2 assumption) and an update rule to optimise the likelihood of your data.

What's really happening here is that you will first produce some $\eta$ which will become a parameter of your exponential family defining it's distribution. The idea is to refine this parameter such that you can pick the $\eta$ with the best chance of producing your dataset. You'll do this with the given update rule

$$
\theta_j = \theta_j + \alpha\sum_ix^{(i)}(y^{(i)} - h_\theta(x^{(i)}))
$$

Take for example logistic regression. Our first choice of $\eta$, which could be 0 for all we know, would define a sigmoid function

![alt text](<sources/Perceptron & Generalized Linear Model/Screenshot 2026-01-16 at 6.08.03 PM.png>)

At each particular value $x$, we'll get the probability of the output data. Then, the idea is to find a line that would maximise the probability of our dataset given their particular input x. You can see this fact more clearly if you consider how we convert bernoulli into the exponential family and how $\eta$ relates to $\phi$. Here each $x^{(i)}$ gives $\eta^{(i)}$ and hence $\phi^{(i)}$.

You can imagine that this sort of reasoning follows given a particular distribution for $y|x$ i.e. A line produces a function which somehow defines the parameter of the probability distribution for a given input $x$.

# Softmax Regression

The procedure is actually quite similar with how we derive logistical regression but instead of bernoulli, we'll go with the multinomial distribution (which is kinda like a generalisation of bernoulli I think?).

Say we have $k$ possible outcomes. We'll define $k - 1$ parameters to describe the possibilities of these outcomes.

> Bernoulli distribution also does the same but $k = 2$

We can then define an equation similar to how we did bernoulli

$$
p(y;\bold{\phi}) = \phi_1^{1\{y=1\}}\phi_2^{1\{y=2\}}\dots\phi_k^{1-\sum_i^{k-1}1\{y=i\}} = \prod_{i}\phi_i^{1\{y=i\}}
$$

where $1\{\dots\}$ is the indicator function and $1\{y=k\} = (1-\sum_i^{k-1}1\{y=i\})$ because we don't have that many parameters and of course $\phi_k = 1 - \sum_i^{k-1}\phi_i$. We can message the equation a bit

$$
\prod_{i}\phi_i^{1\{y=i\}} = e^{\sum_{i}1\{y=i\}\ln(\phi_i)}
$$

At this point, extracting $y$ out to get $T(y)$ is quite complicated because of the indicator function so we'll have to express the expression another way.

If you notice, we're kinda doing a dot product with the values of $y$ and $\phi$. We can define $T(y) \in \R^{k-1}$ to be a vector

$$
T(y_1) = \begin{pmatrix}
1 \\
0 \\
0 \\
\dots
\end{pmatrix}
$$

where the $i$th index of the vector will be $1$ if $y$ has the $i$th outcome. We'll write this as $T(y_i)_i = 1$. Before we can rewrite anything, pay close attention to the dimension of $T(y)$: It's $\R^{k-1}$ and not $\R^{k}$. So we'll first have to write

$$
e^{\sum_{i}1\{y=i\}\ln(\phi_i)} = \exp\left(\ln(\phi_k) + \sum_i^{k-1}1\{y = i\}\ln(\frac{\phi_i}{\phi_k}) \right)
$$

Then we can rewrite

$$
\exp\left(\ln(\phi_k) + \sum_i^{k-1}1\{y = i\}\ln(\frac{\phi_i}{\phi_k}) \right) = b(y)e^{T(y)\cdot\eta - a(\eta)}
$$

where

$$
\eta = \begin{pmatrix}
\ln(\phi_1/\phi_k) \\
\ln(\phi_2/\phi_k) \\
\dots \\
\ln(\phi_{k-1}/\phi_k)
\end{pmatrix}
$$

$$
b(y) = 1
$$

and $T(y)$ defined as before. The issue here would be $a(\eta) = -\ln(\phi_k)$; we have to find a way to use our vector.

To do this, consider the elements of $\eta$ one by one. You'll find that

$$
\ln(\frac{\phi_i}{\phi_k}) = \eta_i\\
\phi_i = e^{\eta_i}\phi_k
$$

Then, we have that

$$
\sum_i^{k-1}\phi_i = 1 - \phi_k = \phi_k\sum_i^{k-1}e^{\eta_i}
$$

Eventually having

$$
\phi_k = \frac{1}{\sum_i^{k}e^{\eta_i}}
$$

where we define $\eta_k = \ln(\phi_k/\phi_k)$. This is nice. Now we have an expression for $a(\eta)$ and thus the hypothesis function

$$
a(\eta) = -\ln(1/\sum_ie^{\eta_i}) = \ln(\sum_ie^{\eta_i})
$$

The hypothesis function for outcome $i$ would be the derivative

$$
\frac{\partial a(\eta)}{\partial\eta_i} = h(\theta_i x) = \frac{e^{\theta_i x}}{\sum_j e^{\theta_j x}}
$$

> You can also get this expression by looking at the $\phi_i = e^{\eta_i}\phi_k$ equation

> We have a set of $\theta_i \in \R^{d+1}$ where $d$ is the dimension of our input data $x$.

I'm omitting the derivation of the update rule here (I've done it before) but the the important thing to realize is that $\theta_i$ is a vector and not an element of a vector (sort of imagine that we're doing $k - 1$ linear regression)

# References

1. https://www.youtube.com/watch?v=iZTeva0WSTQ&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=4
2. https://en.wikipedia.org/wiki/Triangle_inequality
