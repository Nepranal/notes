# Other Types of Hypothesis

With linear regressions, we typically see a hypothesis of the following form:

$$
h(x) = \sum_i \theta_ix_i
$$

However, sometimes, you may want to fit say a quadratic functions instead or some other functions of $x$; the feature.

$$
h(x) = \theta_0 + \theta_1x + \theta_2\sqrt{x}
$$

This function only uses 1 feature but what we will do is to set it as so

$$
h(x) = \theta_0 + \theta_1x_1 + \theta_2x_2
$$

So that $x_1=x, x_2 = \sqrt{x}$. Then we will subject this to the same MSE loss function. What happens then is that your hypothesis will still be the most optimal (once converged) w.r.t the MSE loss function.

# Locally Weighted Regression

This concerns the issue of what if we only want to look at local samples. So, for interpolation, perhaps no one function is good enough to represent the entire training samples. But, you'd find that locally, you can make a pretty good estimation of the function

![alt text](<sources/Locally Weighted & Logistic Regression/Screenshot 2026-01-08 at 11.45.00 AM.png>)

The trick here is to define a weight function to our hypothesis. So, we'll have:

$$
h(x) = \sum_i w_i\theta_ix_i
$$

where

$$
w_i = \frac{(x-x_i)^2}{2\tau^2}
$$

$\tau$ is the hyperparameter of the hypothesis (meaning, you define it yourself). The idea of the weight function is to put certain importance from the set of data that you have. This particular one only look at the data points which are close.

Looking at it another way, $w_i$ defines a "bulge" where the spread of it is controlled by $\tau$

![alt text](<sources/Locally Weighted & Logistic Regression/Screenshot 2026-01-08 at 12.02.29 PM.png>)
In a way, it's very similar to the Gaussian probability distribution function (other than the fact it doesn't integrate to 1).

Locally Weighted Regression is what's called as 'non-parametric' learning algorithm as opposed to linear regression which is a 'parametric' learning algorithm. What this all means is that the former requires you to have storage that grows as the data grows while the latter doesn't.

# Probabilistic Interpretation of Linear Regression

We would like to draw a line that would best fit our data. One way we can do this is to imagine that the data points are perturbed from our line by a random noise. Mathematically we can write

$$
y^{(i)} = \theta^Tx^{(i)} + \epsilon^{(i)}
$$

where $\epsilon^{(i)} \sim N(0, \sigma^2)$ and is iid. You can imagine that one instance of this "line" is probably no more than what seems like a scatter of points.

> iid means independent and identically distributed. This just means that $\epsilon^{(i)}$ and $\epsilon^{(j)}$ is independent from one another and that they have the same distribution

Put it this way, you'll see that $y^{(i)}$ becomes probabilistic in it's nature and so does any set of observations that we have. That is, we could observe our data multiple times from the same input and observe different set of values each with a particular probability for a given line. Furthermore, since the error is gaussian, so will $y^{(i)}$. We can say that

$$
\text{I\kern-0.15em P}(y^{(i)}|x^{(i)};\theta) = \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{1}{2}(\frac{y^{(i)}-\theta^Tx^{(i)}}{\sigma})^2}
$$

And because the $\epsilon$ are i.i.d, so will $y$.

Now, of course we already have our data. What we can do typically is to think about how likely it is for us to observe this set of output with it's input. To do that, since they're i.i.d we can say the probability of them happening (we also call it likelihood):

$$
\mathscr{L}(\theta) = \prod_i \frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{1}{2}(\frac{y^{(i)}-\theta^Tx^{(i)}}{\sigma})^2}
$$

Notice, from our likelihood, we find that it depends only on the line i.e. on $\theta$. What we can reasonably do here is to say that since we observe those data, the line that is being disturbed should be the one that is most likely to produce those data points based on our random noises. In other words, we want to find $\theta$ that will maximise our likelihood (hence why I wrote it so that it takes in $\theta$ as a parameter).

Typically, we put it under the following form

$$
\mathscr{l}(\theta) = \log(\mathscr{L}(\theta)) = \sum_i \log(\frac{1}{\sqrt{2\pi}\sigma}e^{-\frac{1}{2}(\frac{y^{(i)}-\theta^Tx^{(i)}}{\sigma})^2})
$$

This is called the log-likelihood where if you maximise the log-likelihood, you will also maximise the likelihood due to the fact that log is a monotonous function. Pushing it a little more, we'll see that:

$$
\mathscr{l}(\theta) = \sum_i \log(\frac{1}{\sqrt{2\pi}\sigma}) -\frac{1}{2\sigma^2}(y^{(i)} - \theta^Tx^{(i)})^2
$$

As you see, to maximise $\mathscr{l}(\theta)$, you'll have to minimise the square term on the right since it's a negative and that the other term in the sum is a constant. This is the MSE method.

What this all amounts to is that minimizing the MSE is equivalent to searching for a line that has the highest probability of producing our data set given a random noise from said line.

# Logistic Regression

We have seen before the idea of Linear Regression. Logistic regression is similar to the ideas of linear regression but is specialised in dealing with binary classification.

> Using linear regression for binary classification is generally a bad idea. This is because linear regression is easy to be skewed by outliers in the context of binary situation.
>
> ![alt text](<sources/Locally Weighted & Logistic Regression/Screenshot 2026-01-08 at 4.10.39 PM.png>)
>
> Consider that you draw a linear regression for the following data set. What you'd find is that you'll have to treshold what you'll take as true or false (1 or 0). An intuitive thing to do is to draw the line at 0.5. If you include the data point at the most right, you'll see that line skewed, and the boundary stop making sense because you'll see obvious true cases becomes false cases. Logistic regression avoids this.

As with linear regression, we will also have a hypothesis for logistic regression

$$
h(\theta^T x) = \frac{1}{1+e^{-\theta^T x}}
$$

$h$ is called a 'sigmoid' or 'logistic' function. What we want from $h$ is to give us the probability of a certain input to be true or false

$$
\text{I\kern-0.15em P}(1|x^{(i)};\theta) = h(\theta^Tx)\\
\text{I\kern-0.15em P}(0|x^{(i)};\theta) = 1 - h(\theta^Tx)
$$

Or, more compactly

$$
\text{I\kern-0.15em P}(y^{(i)}|x^{(i)};\theta) = (h(\theta^Tx))^y(1-h(\theta^Tx))^{1-y}
$$

where $y$ is constrained to either be 0 or 1. What we can do next is consider the likelihood of the dataset to happen which will depend on our choice of $\theta$.

$$
\mathscr{L}(\theta) = \prod_i (h(\theta^Tx^{(i)}))^{y^{(i)}}(1-h(\theta^Tx))^{1-y^{(i)}}
$$

The log-likehood would be:

$$
\mathscr{l}(\theta) = \sum_i \log((h(\theta^Tx^{(i)}))^{y^{(i)}})+\log((1-h(\theta^Tx^{(i)}))^{1-y^{(i)}})\\
= \sum_i y^{(i)}\log(h(\theta^Tx^{(i)})) + (1-y^{(i)})\log(1-h(\theta^Tx^{(i)}))
$$

we'd like to find a $\theta$ that will make our dataset most probable. The goal then is to find a $\theta$ that will maximise this function which you can do by finding its derivative and by gradient ascent.

$$
\frac{d\mathscr{l}(\theta)}{d\theta_j} = \sum_i \frac{y^{(i)}}{h(\theta^Tx^{(i)})}(\frac{dh(\theta^Tx^{(i)})}{d\theta_j}) - \frac{(1-y^{(i)})}{1-h(\theta^Tx^{(i)})}(\frac{dh(\theta^Tx^{(i)})}{d\theta_j})
$$

With our given hypothesis, we'll find that

$$
\frac{dh(\theta^Tx)}{d\theta_i} = -\frac{1}{(1+e^{-\theta^Tx})^2}\frac{d}{d\theta_i}(1+e^{-\theta^Tx})\\
=-h^2(\theta^Tx)(-e^{-\theta^Tx}x_i)
$$

$$
\frac{dh(\theta^Tx)}{d\theta_i} = x_ih(\theta^Tx)(1-h(\theta^Tx))
$$

Which comes from the fact that $e^{-\theta^Tx} = \frac{1-h(\theta^Tx)}{h(\theta^Tx)}$ by some algebraic manipulation. We can then further simplify the log-likelihood derivative:

$$
\frac{d\mathscr{l}(\theta)}{d\theta_j} = \sum_i x_j^{(i)}y^{(i)}(1-h(\theta^Tx^{(i)})) - x_j^{(i)}(1-y^{(i)})(h(\theta^Tx^{(i)}))\\
= \sum_i x_j^{(i)}(y^{(i)}-y^{(i)}h(\theta^Tx^{(i)}) - h(\theta^Tx^{(i)}) + y^{(i)}h(\theta^Tx^{(i)}))\\
= \sum_i x_j^{(i)}(y^{(i)} - h(\theta^Tx^{(i)}))
$$

Which then leads to our update rule using gradient ascent

$$
\theta_j = \theta_j + \alpha\sum_i x_j^{(i)}(y^{(i)} - h(\theta^Tx^{(i)}))
$$

> Notice that this update rule is practically the same as linear regression except for the definition of $h$...

# Newton Method

This is an alternative to gradient ascent/descent. It's faster on some cases but is also much more difficult to compute. The idea is with this graph:

![alt text](<sources/Locally Weighted & Logistic Regression/Screenshot 2026-01-08 at 9.13.50 PM.png>)

The goal is to find locations for when $f=0$. From a random point, we'll approximate a line using the gradient s.t. that line intersect with the x-axis. So, for a given function $f(\theta)$, we know that

$$
f'(\theta_0) = \frac{f(\theta_0)}{\theta_0 - \theta_1}
$$

Put it another way:

$$
\theta_1 = \theta_0 - \frac{f(\theta_0)}{f'(\theta_0)}
$$

> Notice that if we stepped too big, the method will still go to the location for which $f = 0$.

You can imagine that this would be useful for cases where we have to find extrema of a function where their first derivative is to be set to 0. This method can help you find those locations. For instance, with how we find the the points for when the log-likelihood of the logistic regression function to be 0, we can use this in place of the update rule with gradient ascent

$$
\theta_j = \theta_j - \frac{\mathscr{l}'(\theta)}{\mathscr{l}''(\theta)}
$$

Though it would require computing the second derivative of $\mathscr{l}$.

The sample provided was to deal with these things in 1 dimension. We can generalise the idea to higher dimensional functions though I'm not sure if the same geometrical argument still holds (but at least you can imagine that it does for 2 and 3 dimensional functions). In a more general case, the update rule is as follows:

$$
\theta_{i+1} = \theta_i - H^{-1}\nabla_\theta h
$$

where $H$ is the hessian matrix. The calculation of $H^{-1}$ is what makes this method expensive particularly when there are a lot of features.

# References

1. https://www.youtube.com/watch?v=het9HFqo1TQ&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=3
