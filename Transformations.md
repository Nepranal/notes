# Transformations

# Preserving Direction Vectors

We know that we can use homogeneous coordinates to represents general transformations in 2D. The issue here is simple when the vector that we’re performing transformations on represents a point. But consider the issue for when that vector represents a direction. Let’s take a look at the perspective that we’re moving the origin around the vector:

![Screenshot 2025-09-17 at 1.06.03 AM.png](sources/Transformations%202701aa5d8a0e80dda434e2f0a8a5d998/Screenshot_2025-09-17_at_1.06.03_AM.png)

As you can see, that newly formed vector (the blue one) no longer preserves the correct direction. We would like to preserve the direction of the red vector instead.

![Screenshot 2025-09-17 at 1.08.12 AM.png](sources/Transformations%202701aa5d8a0e80dda434e2f0a8a5d998/Screenshot_2025-09-17_at_1.08.12_AM.png)

Before diving into the solution, consider for a moment what we mean by a vector that represents a direction. Imagine the following:

![Untitled Diagram.drawio (6).png](sources/Transformations%202701aa5d8a0e80dda434e2f0a8a5d998/Untitled_Diagram.drawio_(6).png)

$$
\vec{q}=\vec{p}+\vec{v}
$$

You can also rewrite it as

$$
\vec{v}=\vec{q}-\vec{p}
$$

$\vec{v}$ is what we mean by direction vector.

Intuitively, say we transform $\vec{p},\vec{q},\vec{v}$ purely by a translated transformation $M$:

$$
M=\begin{pmatrix} 1 & 0 & e \\ 0 & 1 & f \\ 0 & 0 & 1 \end{pmatrix} 
$$

What you would want to happen is the same exact scenario in the case where all vectors have yet to be transformed. But this is not the case at all. In fact, you’ll find:

 

$$
M\tilde{q}-M\tilde{p}=\begin{pmatrix} v_1 \\ v_2 \\ 0 \end{pmatrix} =\begin{pmatrix} \vec{v} \\ 0 \end{pmatrix} 
$$

We’re expecting $M\tilde{v}$. This is not remotely close to what it is. Look at it another way, 

$$
M\tilde{p} + M\tilde{v} \neq M\tilde{q}
$$

The issue comes from the fact that, if you actually do the sum, the translations adds up. So, a solution: don’t compute the translation for the direction vector. Typically, for direction vectors, instead of putting a 1, we put 0 as the last entry:

$$
\tilde{v}=\begin{pmatrix} v_1 \\ v_2 \\ 0 \end{pmatrix} 
$$

Applying the transformation to all will now bear the right results under translations. Good news though, this method will work out as well under rotation and scaling.

# Views

Once all of the objects has been translated, the next question would be how to render the scene that has been put together. The answer to this is actually a bit more complicated and this section only answer parts of it: how should the objects be placed.

The solution to this answer would be to imagine a sort of volume through your viewing display

![Screenshot 2025-09-21 at 7.26.18 PM.png](sources/Transformations%202701aa5d8a0e80dda434e2f0a8a5d998/Screenshot_2025-09-21_at_7.26.18_PM.png)

We define what’s called a viewing volume. It’s defined as a square with bounds:

- b: lower-bound x coordinate
- t: upper-bound x coordinate
- …

Similarly with the rest. Any object not inside the bounds won’t be rendered. Once we’re here, we can kinda just ‘flatten’ the 3d volume. What does this mean and how to do it? Well, we’ll deal with those things later.

Before going further, let’s just spell out all the coordinate systems you’ll have to deal with

1. Model space: The coordinate system defined for each individual object
2. World space: The coordinate system of the world
3. Camera space - the view volume / the thing you’re expecting to see

The conversion:

1. Model space → world space: Do normal typical transformations to what you like
2. World space → camera space: first define the camera whereabout and from here you got the transformation for the rest of the objects

There is 1 more coordinate system to transform into: the canonical view volume. This is like a standardised view volume that we saw earlier. It’s defined like such:

![Screenshot 2025-09-21 at 7.37.07 PM.png](sources/Transformations%202701aa5d8a0e80dda434e2f0a8a5d998/Screenshot_2025-09-21_at_7.37.07_PM.png)

The transformations for the previous sets of coordinate systems depends on the whereabouts of where you want things to be in. This one however is fixed so you’d expect something fixed as well. The transformation matrix that can achieve something like this is called an orthogonal projection.

Also, once you’re in the canonical view volume, you can still do the ‘flattening’ things I talked about earlier. It’s just this time you’ll have to do it at the near plane.

## Orthogonal Projection

Going from your own defined view volume to the canonical view volume can be done by the following matrix:

$$
\begin{bmatrix}
\frac{2}{r-l} & 0 & 0 & \frac{-2l}{r-l}-1 
\\0 & \frac{2}{b-t} & 0 & \frac{-2b}{b-t}-1 
\\ 0 & 0 & \frac{2}{f-n} & \frac{-2n}{f-n}-1
\\ 0 & 0 & 0 & 1\end{bmatrix}
$$

Let’s decode this for a little bit. 

- The last row of the matrix operation is to preserve the last entry of the homogenous equation.
- To go from camera to canonical view, you don’t have to rotate but you may have to scale. So the upper 3x3 portion of the matrix is just a diagonal matrix.

The rest of the numbers that you see are there to scale and translate the coordinate so that the frame is in the middle of the volume. Everything will still be the same. We’re just translating to another system.

## Perspective Projection

Orthogonal projection is great, but, it’s not quite how we perceive things with our eyes. Notice that in none of the steps in the derivation do we consider the size of the object or how the human eyes actually work. Perspective projection is there to do that.

This is how we model the human eye:

![Screenshot 2025-09-21 at 7.50.26 PM.png](sources/Transformations%202701aa5d8a0e80dda434e2f0a8a5d998/Screenshot_2025-09-21_at_7.50.26_PM.png)

This is the new view volume that we’re considering. 

Okay, but then what? Do we just do something similar to the orthogonal projection and find a canonical frustum? Nope. We can still reuse the canonical view volume stuff. It’s kinda hard to explain how it works, but just imagine that each ray is supposed to carry some information and we need to just display them somehow at a flat surface. Then making them parallel again hopefully will make sense

We can achieve this by using the following transformation:

$$
\begin{bmatrix}
n & 0 & 0 & 0
\\0 & n & 0 & 0
\\ 0 & 0 & n+f & -nf
\\ 0 & 0 & 1 & 0\end{bmatrix}
$$

Just a couple of things about this transformation, for the derivation of which, kindly look at the related links. The transformation is meant to only change the x and y values while preserving z (we’re not changing the depth of the scene) but doing so is kinda difficult so here we’re approximating instead. The bound of the values are still correctly i.e. if you’re at any of the near or far planes, you’ll stay there, but z will indeed be changed.

# References

https://www.youtube.com/watch?v=EKN7dTJ4ep8&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=6

https://www.youtube.com/watch?v=1z1S2kQKXDs&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=8