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
x\cos(\Theta) + y\sin(\Theta) = L_1\cos\theta_2 + L_2\tag{3}
$$

$$
-x\sin(\Theta) + y\cos(\Theta) = -L_1\sin\theta_2\tag{4}
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
A = L_1\cos\theta_2 + L_2\\
B = -L_1\sin\theta_2
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

Now we'll have 3 cases tp consider:

$$
\Theta = \begin{cases}
      \arctan(\frac{A}{B}) & B>0 \\
      \pi + \arctan(\frac{A}{B}) & B<0 \\
      \frac{\pi}{2}, \frac{3\pi}{2} & B=0
   \end{cases}
$$

where for $B=0$ depends on A's sign

> Don't sum and repeat the same technique. The solution to that new equation won't necessarily be equal to it's part

Now, what if $y=0$? Then we'll have

$$
x\cos(\Theta) = A\\
-x\sin(\Theta) = B
$$

Which we can rewrite as

$$
x\cos(-\Theta) = x\cos(\alpha) = A\\
x\sin(-\Theta) = x\sin(\alpha) = B
$$

where $-\Theta = \alpha$ and the solution is similar to before

$$
\Theta = \begin{cases}
      \arctan(\frac{B}{A}) & A>0 \\
      \pi - \arctan(\frac{B}{A}) & A<0 \\
      \frac{\pi}{2}, \frac{3\pi}{2} & A=0
   \end{cases}
$$

# 2D Inverse Kinematics As Intersecting Circles

The previous section presented a rather complicated approach to reach a solution since it involves solving a system of trigonometric functions. We can pursue a simpler solution by considering an intersection of circles.

Consider a case of 2 links trying to reach a point $\vec{x}$. We can draw an imaginary circle of radius $L_2$ around that point.

![alt text](<src/Forward-Backward Kinematics/intersection.jpeg>)

Now, if our first link can be at any point on that circle, we can definitely reach $\vec{x}$ by adjusting the 2nd link accordingly. Since the first link moves in a circle, our goal then is to solve for the intersection created by the two circles. In other words, solve:

$$
x^2+y^2=L_1^2 \tag{6}
$$

$$
(x-x_1)^2+(y-x_2)^2=L_2^2 \tag{7}
$$

where $\vec{x} = [x_1,x_2]$. This is hopefully bit simpler than the trigonometric system

By expanding $(7)$, we'll get

$$
x^2-2x_1x+x_1^2+y^2-2x_2y+x_2^2 = L_2^2
\\\,\\
x^2+y^2-2(x_1x+x_2y)+x_1^2+x_2^2 = L_2^2
$$

After some rearrangement and using the first equation:

$$
x_1x+x_2y = \frac{L_1^2 - L_2^2 + |\vec{x}|^2}{2} = k
$$

Which is an equation for a line. If we assume $x_2\neq0$, then we can get a quadratic equation

$$
x^2 + \left(\frac{k-x_1x}{x_2}\right)^2 = L_1^2
\\\,\\
(x_2x)^2+k^2-2x_1kx+(x_1x)^2=(L_1x_2)^2
\\\,\\
x^2|\vec{x}|^2-2x_1kx + k^2 - (L_1x_2)^2 = 0
$$

The determinant of such an equation being

$$
\Delta = 4x_1^2k^2 - 4|\vec{x}|^2(k^2-L_1^2x_2^2) = 4(x_1^2k^2-|\vec{x}|^2k^2+|\vec{x}|^2L_1^2x_2^2)\\
\\\,\\
= 4(|\vec{x}|^2(L_1^2x_2^2) - x_2^2k^2) = 4x_2^2(L_1^2|\vec{x}|^2-k^2)
$$

> If you write out the quadratic solution in full, you'll find that the factor of 4 can be removed

If $x2=0$, then $x = k/x_1$ and $y = \plusmn\sqrt{L_1^2-x^2}$

Once we've solved for $x$ and $y$, we can easily get $\theta_1$ because

$$
\tan(\theta_1) = \frac{y}{x}
$$

(given non-zero x)

If we can solve for $\theta_1$, then we can solve for $\theta_2$ as well by following similar procedure. However, we'll have to transform $\vec{x}$ w.r.t the end of the first link's end affecter. This, can be done by the following matrix:

$$
\begin{bmatrix}
\cos(\theta_1) && \sin(\theta_1) && -L_1\\
-\sin(\theta_1) && \cos(\theta_1) && 0\\
0 && 0 && 1
\end{bmatrix}
$$

This idea of circles I believe can be extended to 3D cases (spheres instead of circles) as well as with a more general case of $n$ links robot arm.

# References

1. https://www.youtube.com/watch?v=_8T7RjXL07M
2. https://www.youtube.com/watch?v=8D0sO8mymQ8
