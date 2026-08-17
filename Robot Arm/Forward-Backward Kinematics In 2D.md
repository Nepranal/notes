# Forward Kinematics In 2D

Forward Kinematics is the process for which we discover find the position of the end affecter $M$ w.r.t the fixed frame $F$. Here is the convention we'll be using

![alt text](<src/Forward-Backward Kinematics/Screenshot 2026-06-10 at 11.04.11 PM.png>)

Here you'll see that each joints is labelled to be an end affecter: $M_1,M_2,M_3$. The final goal is to figure out what $X$ is given you know $x,L_1,L_2,\theta_1,\theta_2,\theta_3$.

Before going forward, it's good to note of the transformation matrix

$$
\tilde{R} = \begin{bmatrix}
\cos(\theta) && -\sin(\theta) && x\\
\sin(\theta) && \cos(\theta) && y\\
0 && 0 && 1
\end{bmatrix}
$$

This will transform a homogenous vector by an angle $\theta$ anti-clockwise (hopefully you'll see how this relates to why we define all angles to be anti-clockwise) and translate it by $x$ in the x-axis and similarly in the y.

The main idea is to transform the origin into the joints of the links (the dots) and eventually ends up at the last join. To do this, we'll define an inverse process since the naive approach is a little messy. The idea is to get the last join back to the origin of $F$. Note that at this point we're ignoring $x$.

We'll start with the first link. We can rotate the first link by $-\theta_1$ and **displace** it by $-L_1$. In matrix terms:

$$
\tilde{T}_{1\_F} = \begin{bmatrix}
1 && 0 && -L_1\\
0 && 1 && 0\\
0 && 0 && 1
\end{bmatrix}
\begin{bmatrix}
\cos(\theta_1) && \sin(\theta_1) && 0\\
-\sin(\theta_1) && \cos(\theta_1) && 0\\
0 && 0 && 1
\end{bmatrix}\\
\,\\
= \begin{bmatrix}
\cos(\theta_1) && \sin(\theta_1) && -L_1\\
-\sin(\theta_1) && \cos(\theta_1) && 0\\
0 && 0 && 1
\end{bmatrix}
$$

> Note that $L_1$ is a displacement and NOT the distance

The neat thing about this transformation is that it places $\theta_2$ to be a measure of angle from the x-axis to the 2nd link which we can once again do a similar transformation and get $\tilde{T}_{2\_1}$ ($2\_1$ here because the angle is originally defined w.r.t the first link).

For the case of our 2 links case, we'll have that

$$
\tilde{T}_{3\_2}\tilde{T}_{2\_1}\tilde{T}_{1\_F}\tilde{X}_{M_3} = \tilde{T}^{-1}\tilde{X}_{M_3} = \tilde{0}
$$

where the zero vector is defined within $F$ and $X_{M_3}$ is the coordinate of $M_3$'s origin w.r.t $F$. What this gives us is a transformation $\tilde{T} = (\tilde{T}_{3\_2}\tilde{T}_{2\_1}\tilde{T}_{1\_F})^{-1}$ to transform from $F$ to $M_3$. This means if we have a point $x$ in $M_3$, it's equivalent $X$ inf $F$ would be:

$$
\tilde{X} = \tilde{T}\tilde{x}
$$

Just for completeness sake, $\tilde{T}$ is going to be:

$$
\tilde{T} = \begin{bmatrix}
\cos(\theta_1) && -\sin(\theta_1) && L_1\cos(\theta_1)\\
\sin(\theta_1) && \cos(\theta_1) && L_1\sin(\theta_1)\\
0 && 0 && 1
\end{bmatrix}
\begin{bmatrix}
\cos(\theta_2) && -\sin(\theta_2) && L_2\cos(\theta_2)\\
\sin(\theta_2) && \cos(\theta_2) && L_2\sin(\theta_2)\\
0 && 0 && 1
\end{bmatrix}
\begin{bmatrix}
\cos(\theta_3) && -\sin(\theta_3) && 0\\
\sin(\theta_3) && \cos(\theta_3) && 0\\
0 && 0 && 1
\end{bmatrix}
$$

In a more generic case with $n$ links we can write $\tilde{T}$ as:

$$
\tilde{T} = \begin{bmatrix}
\cos(\Theta) && -\sin(\Theta) && \sum^{n-1}_{i=1} L_i\cos(\sum^i_{k=1}\theta_k)\\
\sin(\Theta) && \cos(\Theta) && \sum^{n-1}_{i=1} L_i\sin(\sum^i_{k=1}\theta_k)\\
0 && 0 && 1
\end{bmatrix}
$$

where $\Theta = \sum^n_{i=1}\theta_i$

# Inverse Kinematics In 2D

Inverse Kinematics deal with finding the reverse problem. Given that you know $X$, how can you figure out the other parameters? Within the field of robotics, we're particularly interested in the case for which $x = \vec{0}$ along with the last angle being $0$.

$$
X = \tilde{T}\vec{0}\tag{1}
$$

we can also view this as null space checking

$$
\tilde{T}^{-1}X = \vec{0}\tag{2}
$$

In the case for which you have 2 links, you can somewhat manage and solve this system of equations. It however gets a little more complicated as you have more links as you now have more unknowns than knowns. We'll deal only with 2 links here

If you write out $(2)$, for a given $(x,y)$ in $F$, you'll have the following system

$$
x\cos(\Theta) + y\sin(\Theta) = -L_1\cos\theta_2 - L_2\tag{3}
$$
$$
-x\sin(\Theta) + y\cos(\Theta) = L_1\sin\theta_2\tag{4}
$$

These are systems of non-linear equations so solving them is a little tricky. We'll start by squaring both equations and adding them together. You'll get

$$
x^2 + y^2 = L_1^2\cos^2\theta_2 + 2L_1L_2\cos\theta_2 + L_2^2 + L_1^2\sin^2\theta_2\\
= L_1^2 + L_2^2 + 2L_1L_2\cos\theta_2
$$

For which then we'll get an expression that $\theta_2$ must obey

$$
\cos\theta_2 = \frac{x^2+y^2-(L_1^2+L_2^2)}{2L_1L_2} \tag{5}
$$

given that $L_1,L2\neq0$. If either one is zero, this case becomes a one link problem and the solution for that is just $\arctan\frac{y}{x}$.

At this point, it'd be nice to set the constants that we get from $\theta_2$ as some variable

$$
A = -L_1\cos\theta_2 - L_2\\
B = L_2\sin\theta_2
$$

so our equations become

$$
x\cos(\Theta) + y\sin(\Theta) = A
$$
$$
-x\sin(\Theta) + y\cos(\Theta) = B
$$

To solve such a system we'll use substitution by imagining an imaginary angle $\phi$. We'll have $r = \sqrt{x^2+y^2}$ and $\tan\phi = x/y$ assuming $y\neq0$. This allows us to get

$$
\sin\phi\cos(\Theta) + \cos\phi\sin(\Theta) =\sin(\phi+\Theta)= A/r
$$
$$
-\sin\phi\sin(\Theta) + \cos\phi\cos(\Theta)=\cos(\phi+\Theta) = B/r
$$

we can sum these and use the same approach to give

$$
\sin(\Theta+\phi+\alpha) = \frac{A+B}{r\sqrt{2}} \tag{6}
$$

Where $\tan\alpha = 1$.

Now, what if $y=0$? Then we'll have 

$$
x\cos(\Theta) = A
$$
$$
-x\sin(\Theta) = B
$$

if we take the sum of our system

$$
x\cos\Theta - x\sin\Theta = A+B
$$

Solving this with substitutions like before, we can get

$$
\cos(\alpha + \theta) = \frac{A+B}{x\sqrt{2}}\tag{7}
$$

# References

1. https://www.youtube.com/watch?v=_8T7RjXL07M
2. https://www.youtube.com/watch?v=8D0sO8mymQ8
