# Bias And Variance

So far, I think we only have a vague meaning of these terms and here we'll use them to describe the behaviour of some learning algorithm. To give some simple visualization, the discussion will be done on polynomial functions

![alt text](<sources/Data Splits, Models & Cross-Validation/Screenshot 2026-04-05 at 5.17.13 AM.png>)

When an algorithm has a high bias, it means that we're baking into the algorithm some kind of assumption that rejects certain other types of models. In the illustration above, the data would've suggest that the line should've been more curved and potentially would have asked it to be more curved with more data. But, because of bias, we see those data as kind of like outliers and ignore them. This may lead into underfitting

When an algorithm has a high variance, it means that it follows the data too exactly. If you have just a bunch of scatter point, the best line you may draw could've been a straight line. If your algorithm has a high variance however, you'll follow each point such that your function will hit most, if not all, points. This would include the outliers as well. Having a high variance may lead into overfitting. This is bad in situations where your data fluctuates (noise, etc.)

# Regularization

We saw this earlier with SVMs. To translate into this discussion, the reason we added regularization was so that our svm won't be too sensitive to data distribution i.e. Lowering the variance of our svm

Regularization is actually a general technique and you can apply it to other algorithms as well although you'll have to take care of the regularization constant.

Regularization can be viewed as a way for us to incorporate prior knowledge of the parameters in our estimation. Take for example logistic regression. Instead of considering $p(S|\theta)$, we consider $p(\theta|S)$ where $S$ is the training set because $\theta$ now has a distribution.

> The way I think about this is that we're now asking about probability of $\theta$ that is most likely to generate $S$.

A reasonable $\theta$ would be the one that maximizes that quantity

$$
\argmax_\theta p(\theta|S) = \argmax_\theta \frac{p(S|\theta)p(\theta)}{p(S)} = \argmax_\theta p(S|\theta)p(\theta)
$$

if logistic regression, we say that $p(S|\theta) = \prod_ip(y^{(i)}|x^{(i)};\theta)$.

If $\theta$ is a gaussian, then you'll get logistic regression + regularization term as the solution to the maximisation problem.

> I take it that other algorithms also has something similar that allows you to derive the regularization term out

This technique is called MAP - Maximum a posteriori.

# Comparing Algorithms

In general, there are 2 sets of data we should care about: training and everything else. When you run your model then, there are 2 things to worry about: training error and generalisation error. Bias and variance is related to these 2 quantities

![alt text](<sources/Data Splits, Models & Cross-Validation/Screenshot 2026-04-05 at 6.51.02 AM.png>)

Here, the graphs can be split into two region. The point of lowest generalisation error is the 'just right'. The best algorithm goes here. To the left of it, is the region for high bias algorithms and to the right algorithms with high variance.

With respect to the training error, you'll see that you simply need to increase the variance of the model to decrease it. However, most of the time doing so won't necessarily correspond to a lower generalization error.

It should be noted here that increasing the variance of an algorithm won't necessarily mean it will have low bias. It's completely possible to have an algorithm that has both high bias and variance

> Although, I think that increasing the variance will generally decrease the bias of the algorithm

Regularization has a similar thing: Have too high of a constant, and you'll underfit. Too low, and you may overfit.

Picking constants and algorithms can be a challenging task. One thing we know for sure however is that we want to be able to find one with the best performance. There are methods for us to determine so starting with splitting the datasets

Observed data are usually partitioned into 3 parts: train/dev/test. The usual workflow is to train and then test your model on dev, and then pick the best from dev. The extra test set is to benchmark your model. With this, we usually have it so that there's about a 60 - 20 - 20 split although there are usually more data contributed to training and dev than test.

These are used under the assumption that you have a lot of data. What if you don't? Then you can typically use something called k-fold cross validation or it's extreme version: leave one out cross validation. These techniques are ways to package your data, basically.

# Feature Selection

How can we decide which features should we actually use for our model? A way is to first take a feature, train and test, see the result. Then to take the 2nd one, pick again from the bucket, and choose that improves your result the most. Keep on doing this until you're satisfied or no more improvements can be observed or no more features to pick

With each feature, we'll define a parameter. This means that in the beginning, we'll have only 1 parameter: the constant term

$$
h(x) = \theta_0
$$

As time goes on, you can add more

$$
h(x) = \theta_0 + \theta_1x_6
$$

(in this case, we've determined that $x_6$ is the thing that improves the error the most)

This procedure is called forward search. It's complement, backward search, removes features one by one

# References

1. https://www.youtube.com/watch?v=rjbkWSTjHzM&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=8
