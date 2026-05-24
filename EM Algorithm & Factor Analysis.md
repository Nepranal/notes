# EM As Coordinate Ascent

The EM algorithm is a kind of coordinate ascent. The idea is that we first optimize w.r.t a particular variable, then optimize again with another. We can write the objective function as

$$
J(\theta, Q) = \sum_i\sum_{z^{(i)}}Q_i(z^{(i)})\log\frac{p(x^{(i)}, z^{(i)})}{Q_i(z^{(i)})}
$$

What we first do is we find a value of $Q_i(z^{(i)})$ (focusing on $Q$), then we maximize the expression w.r.t to $\theta$ (focusing on $\theta$)

# Problems With Mixture of Gaussian

Mixtures of gaussians work great for when there some distinct clusters you can recognise. This usually happens when you have lots of data. Let's say number of samples as $m$ and number of features as $n$. The issue is, if you don't have sufficient data e.g. $n \geq m$ or $n \approx m$, then the model may not work as well.

Imagine fitting 2 data points in 2d space
![alt text](<sources/EM Algorithm & Factor Analysis/Screenshot 2026-05-23 at 11.41.25 AM.png>)

What will happen is the gaussian contour will be spread infinitely thin forming a line on the 2 data points. Here you see that if you're uncertain that the data are not grouped together, the outcome may be unpredictable or may not be within expectations.

Another aspect to consider, and really the main thing, is that if $n \geq m$, then $\Sigma$ will be non-invertible which makes the computations invalid

# Fixing The Non-Invertible Problem

Let's just say we don't care about the outcome, and we would like to just for the thing to work. One way we can approach this is to constraint $\Sigma$ such that it will be invertible.

One thing we can do is to force it to be diagonal i.e. Assuming there is no correlation between the variables. This is a little silly since most things could have some correlation. Though such a situation may arise from time-to-time

Even sillier is to assume everyone has the same error / standard deviation

$$
\Sigma = \begin{bmatrix}
\sigma^2 & 0 & 0&\dots\\
0 & \sigma^2 & 0 & \dots\\
0 & 0 & \sigma^2 & \dots\\
&\dotsb
\end{bmatrix}
$$

Maybe silly, but our computations will work now

So far, we've taken to some extremes: 1. We don't know the correlation, make no assumption. 2. No such correlations. 3. No such correlations and everyone have the same variation. Perhaps there's a middle ground we can take?

# Factor Analysis

This time, let's define $z$ to be continous instead of discrete. Particulalry, $z \isin \R^d \sim N(0,I)$, ($d<n$). We'll also say that

$$
x = \mu + \Lambda z + \epsilon
$$

where $\Lambda \isin \R^{n\times d}$ is some sort of matrix, $\mu \isin \R^n$, and $\epsilon \isin \R^n \sim N(0, \Psi)$. We will also assume that $\Psi$ is diagonal. Effectively, we're saying that

$$
x|z \sim N(\mu + \Lambda z, \Psi)
$$

What we're modelling, basically, is we imagine that there are some driving forces that make the sample $x$ to be what they are. $z$ is a way for us to quantify and represent those 'factors' to be some vector of numbers with $\Lambda$ defining a weightage

Then, we also say that there are some sort of error term associated with each element of the sample. The fact that $\Psi$ is diagonal means those associated errors are independent from one another.

## Data Distributions By Factor Analysis

Graphically, lets say that $x, \mu, \epsilon \isin \R^2$, $z \isin \R^1$. Then, a sample may look like (the red crosses):

![alt text](<sources/EM Algorithm & Factor Analysis/Screenshot 2026-05-23 at 1.09.46 PM.png>)

This demonstration shows the type of patterns we can capture with factor analysis. Typically, if your data has high dimensionality but can be described in lower dimensions, then the method can be pretty good. In this case, data is 2D, but seems to be grouping along a line in the plane. The "offsets" is described by the gaussians with $\mu + \Lambda z$ as it's mean with variance $\Psi$.

Notice, this is kinda similar to linear regression...

# EM For Factor Analysis

We first need to figure out $p(x,z)$ for any given $z$. To do this, we will first consider a trick for considering joint variables. Instead of finding $p(x,z)$ directly, we'll consider

$$
\begin{bmatrix}
z\\
x
\end{bmatrix} \sim N(\mu_{xz}, \Sigma_{xz})
$$

where we stack $z, x$ on top of one another. There's some nifty tricks you can do but essentially, the mean would be

$$
\mu_{xz} = \begin{bmatrix}
0\\
\mu
\end{bmatrix}
$$

where the top $0$ (which is a vector) is because $z$ has a zero mean and the bottom being that

$$
E(x) = E(\mu + \Lambda z + \epsilon) = E(\mu) + E(\Lambda z) + E(\epsilon) = \mu
$$

since $E(\epsilon) = 0$. The reason as to why you can do this is due to marginal distribution i.e.

$$
\int_0^1 p(x,z) dz = p(x)
$$

You can expand $p(x,z)$, take the integral, and you'll form another gaussian with $p(x)$. For now just know that if the joint ditribution is a gaussian, and each variables are gaussians, then you can stack the mean if you stack the variables.

The other difficult part is finding $\Sigma_{xz}$ which is

$$
\Sigma_{xz} = \begin{bmatrix}
I && \Lambda^T\\
\Lambda && \Lambda\Lambda^T + \Psi
\end{bmatrix}
$$

Alright, we've found the parameters of our distribution, what's left is to optimise them e.g. Doing MLE to the log lkelihood. Unfortunately, we have no idea how to do this so we will resort to using EM

Luckily for us, most of the EM steps would still be valid since we're just changing from a sum to an integral (this time $z$ is continuous). We still need to pick a value however

For the E step, we will have to get $Q(z) = p(z|x)$ which is basically figuring out the parameter for

$$
z|x \sim N(\mu_{z|x}, \Sigma_{z|x})
$$

You can get an expression of both parameters by considering $\mu_{xz}$ and $\Sigma_{xz}$ (I won't write them down here)

For the M step, you'll have to

$$
\max_\theta \sum_i\int_0^1 Q_i(z^{(i)})\ln\frac{p(x,z)}{Q_i(z^{(i)})} dz^{(i)}
$$

You can expand each terms and evaluate the integral. Then you'll take the derivative of the resulting expression w.r.t each parameters

> Easier said than done.

The rest would follow the usual procedure for EM

# References

1. https://www.youtube.com/watch?v=tw6cmL5STuY&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=15
