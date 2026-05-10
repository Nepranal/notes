# Optimising Margin

The problem described with the Support Vector Machine is essentially finding a decision boundary that is as far as possible from either type of values we're predicting.

![alt text](<sources/Kernels/Screenshot 2026-03-22 at 7.51.35 PM.png>)

Imagine we draw a line to represent the distance for both types and for each point. The 'optimal' line we define here to be the line with the biggest geometrical margin. Mathematically we can write:

$$
\max_{\gamma,\omega, b}\gamma\\
\,\\
s.t. \ \frac{y^{(i)}(\omega x^{(i)} + b)}{||\omega||} \geq \gamma, i=1,\dots,N\\
||\omega|| = 1
$$

$||\omega|| = 1$ is so that the functional margin and the geometric margin is equal. This form of the problem is actually no good because of the non-convex constraint $\omega$ has. So let's convert this into

$$
\max_{\gamma,\omega, b}\frac{\hat{\gamma}}{||\omega||}\\
\,\\
s.t. \ \frac{y^{(i)}(\omega x^{(i)} + b)}{||\omega||} \geq \gamma, i=1,\dots,N
$$

We're going to pull some doohickey to help us solve this because this form of the problem is still non-convex ($\hat{\gamma}/||\omega||$).

The first thing we'll do is to constraint $\hat{\gamma} = 1$ which is just another way of saying a scaling constraint put on $\omega$ and $b$. We can do this because of scale invariance of the geometric margin i.e. we can just give it some arbitrary scale and it won't affect the optimisation problem because we can just rescale everything at the end.

Doing so allows us to have the following form of the problem:

$$
\min_{\gamma,\omega, b}\frac{1}{2}||\omega||^2
\\\
\\\
s.t. \ y^{(i)}(\omega x^{(i)} + b) \geq 1, i=1,\dots,N
$$

> $max$ changed to $min$ because we flipped the fraction. $||\frac{\omega^2}{2}||$ instead of $||\omega||$ has the same principle as to why we can optimise log-likehood instead of just the likelihood

## Representer Theorem

The other thing we're going to make use of is something called the Representer Theorem. The basis of using this is to handle for the case when our feature vectors get very big.

The theorem is actually quite simple. It says that $||\omega||$ can be represented as a linear combination of the features (at least when it is at the ideal solution). In other words

$$
\omega = \sum_i \alpha_i y^{(i)}x^{(i)}
$$

> $y^{(i)} \isin \set{1,-1}$ so it is still a linear combination. This particular form will be used in a bit

The proof for this thing is quite complicated but there's some nice intuition that can be laid out. To see why this should be true, take our update step for logistic regression

$$
\theta_i \colonequals \theta_i - \alpha\sum_i(h_\theta(\omega x) - y^{(i)})x^{(i)}
$$

You see that no matter what, we are always using some multiples of the input feature. If you explicitly write the update step continuously, you'll find that you can just factor them all out into some multiple of the input feature.

The important part of this is that you can write everything in terms of dot products. You'll see why this is important

# The Kernel Trick

An issue with this method is that we're left wondering with the case of what if the data are not separable? Well then the method won't work but is there something else we can do? Turns out, there is

A way to resolve this issue is to raise the dimensionality of your data. If they're not separable in 2D, then perhaps 3D or 4D.

![alt text](<sources/Kernels/Screenshot 2026-03-22 at 8.40.43 PM.png>)

Well then, here's the question: How can we do so? The answer turns out to be simple (well, initially). We can just add different combinations of features we already have. For example, if we have $x = \begin{pmatrix}
    x_1,x_2
\end{pmatrix}$ then our mapping function $\phi$ could be

$$
\phi(x) = \begin{pmatrix}
    x_1\\
    x_2\\
    x_1x_2\\
    x_1+x_2\\
    \dots
\end{pmatrix}
$$

and you can do this for as many features as you like.

There is however a constraint: computation. Perhaps the bigger your feature vector the better but a bigger feature would mean that you'll have a hard time processing it.

Before we go any further, let's discuss a bit about the kernel trick. The 'trick' actually revolves around processing the dot product efficiently. The context of using it is that if you can express your algorithm in terms of dot products, then you can freely, to a degree, scale your features as big as you like with the only caveat being: can you find a way to compute your dot products efficiently? In other words, if:

1. You can express your algorithms in terms of dot products
2. You can find a kernel for such a feature

then you can use the kernel trick. The SVM is actually an optimal margin classifier along with the kernel trick i.e. replace all $x$ with $\phi(x)$ in the classifier.

# Kernels

This section will discuss some typical kernels you may see. The first one we can do is if we have this feature vector:

$$
\phi(x) = \begin{pmatrix}
    x_1x_1\\
    x_1x_2\\
    \dots\\
    x_nx_n
\end{pmatrix}
$$

We can express $K(x,z) = <\phi(x), \phi(z)> = (x^Tz)^2$. This is great because instead of having $O(n^2)$ we can instead have $O(n)$ computation complexity where $n$ is the dimension of $x$.

> The reason why naively this is $O(n^2)$ is because it's basically 2-permutation of the incoming feature. If we compare with the amount of thing you have to do with the original vectors, you get way less terms. Essentially, it will take you $O(n^2)$ operations just to generate $\phi(x)$.

There's also a little more to this: $K(x,z) = (x^Tz + c)^2$ which corresponds to the following feature

$$
\phi(x) = \begin{pmatrix}
    x_1x_1\\
    x_1x_2\\
    \dots\\
    x_nx_n\\
    \sqrt{2c}x_1\\
    \sqrt{2c}x_2\\
    \dots\\
    c
\end{pmatrix}
$$

Same deal: more fatures, but same complexity of $O(n)$. You can even take this further and have $K(x,z) = (x^Tz + c)^k$ which allows you to have monomials all the way to order k (basically, imagine that you have $d$ elements and you have to pick $k$ of them. The sum of the exponent will always be $k$ except for some; the $\sqrt{2c}$ terms won't have).

# Kernels As A Similarity Measure

The definition of a kernel is based on dot products. As such we can also see it as a sort of similarity measure between 2 vectors: if $\phi(x)$ and $\phi(z)$ are similar, then $K(x,z)$ should be big and small otherwise.

Here's a question: Is it okay to use any function as long as it follows this particular view? Well, kind of as long as the original definition of $K$ is still satisfied i.e. Still a dot product. Otherwise, your derivations may fall apart. (so for the most part, no).

## Gaussian Kernel

The gaussian kernels defines $K$ to be

$$
K(x,z) = e^{-\frac{||x-z||^2}{\sigma^2}}
$$

This represents a feature vectore of infinite dimensions

## Softer Margin

So far we have assume that the data are completely separable. And when we think they could not, we push it further and added extra dimensions. While increasing the dimension of your data could improve the 'separableness', what if there is indeed an instance of your data which cannot be separated? The solution is to introduce softer margins i.e. to allow some data points to be above or below the separating hyperplane.

The actual term for this technique is called regularization. To add regularization into our problem, we'd do

$$
\min_{\gamma,\omega, b} \frac{1}{2}||\omega||^2 + C\sum_i\xi_i\\
s.t. \ y^{(i)}(\omega x^{(i)} + b) \geq 1 - \xi_i, \ i=1,\dots,N\\
\xi_i\geq0
$$

As you can see here, we're relaxing the functional margin a little bit by allowing the distance of the value to be smaller than usual. You can still solve this with available optimization packages or using SMO.

Oh, and another reason why you might want to add a margin is because the margin classifier can be very sensitive to outliers and you may want to not have those be critical to the classification

![alt text](<sources/Kernels/Screenshot 2026-04-01 at 6.58.04 AM.png>)

# References

1. https://www.youtube.com/watch?v=8NYoQiRANpg&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=7
2. https://www.youtube.com/watch?v=OdlNM96sHio
