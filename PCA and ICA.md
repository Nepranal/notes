# PCA

The idea is reduce the dimensionality of data say if you suspect your data can be represented at a lower dimension. To do this, one approach is to find a line of best fit (in higher dimensions I guess it would be a hyperplane). PCA tries to find a hyperplane that minimizes the square distance of our data points to the plane

> Almost kinda like linear regression

![alt text](<sources/PCA and ICA/Screenshot 2026-05-26 at 8.35.22 PM.png>)

Another way to look at PCA is that we're trying to find a line that preserves the variability of the data as much as possible i.e. maximizes the distance between the projections of the data points

Mathematically, say that the line we're looking for is represented by a vector $\vec{u}$ and that $|\vec{u}| = 1$. The projection of the data point $i$ $x^{(i)} \isin \R^n$ will be $u \cdot x^{(i)}$. Our goal then is simply

$$
\max_{\vec{u}}\sum_i (x^{(i)} \cdot \vec{u})^2 = \max_{\vec{u}} \sum_i (\vec{u}x^{(i)}) (x^{(i)}\vec{u}) = \max_{\vec{u}}\vec{u}^T\left(\sum_i x^{(i)}x^{(i)T} \right)\vec{u}
$$

If we let $\Sigma = \sum_i x^{(i)}x^{(i)T}$, then we can rewrite it to be

$$
\max_{\vec{u}} \vec{u}^T\Sigma\vec{u}
$$

The solution to this can be found using methods of lagrange multiplier and it turns out $\vec{u}$ is an eigenvector of $\Sigma$.

Now, what we will do is if we want our new representation going from $n$ to $k < n$ dimensions, then we first find the top $k$ eigenvectors $u_1,u_2,\dots,u_k$ (ordered by the eigenvalues) and then we define

$$
y = \begin{bmatrix}
    u_1x\\
    u_2x\\
    \dots\\
    u_kx
\end{bmatrix}
$$

What this essentially means is that we transform it while keeping our original data's variability per dimension. And you can also show that this choice of basis maximizes $\sum_i y^{(i)}$.

There's also another interpretation about this transformation is that we've chosen a new basis for our data defined by $u_1, u_2, \dots, u_k$

One last thing: we also do data preprocessing (normalization). The reason being that PCA is very sensitive to noise. Here are some sample cases as to why

![alt text](<sources/PCA and ICA/Screenshot 2026-05-26 at 10.09.09 PM.png>)

For the 2nd column, since the scale is so different, from a perspective the `km` axis , PCA might just see there to be no variation at all (since the variation is small w.r.t the 2nd axis (I think))

# References

1. https://www.youtube.com/watch?v=dyb_cFywuik&list=PLoROMvodv4rMiGQp3WXShtMGgzqpfVfbU&index=16
