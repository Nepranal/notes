# Smooth Shading

Our previous discussion of shading had not considered what would happen if the size of our mesh is big. Imagine you have a single light source and a very big mesh with only 1 face below that light source. The shadow of the mesh should vary depending on it's distance to the light source. However, the calculation for the shading accounts only on the surface normal. With only 1 face, you'll only have 1 surface normal and so your shading won't work as well. This can also happen with an approximate sphere:

![alt text](<sources/Shading Transformations/Screenshot 2025-12-03 at 8.05.41 PM.png>)
As you'll see, it's not so smooth

# Gouraud Shading

There are multiple ways to resolve such a problem. Goroud Shading considers using vertex normals instead of surface normal

> Vertex normal can be calculated by getting a weighted average of all the surface normales of the serufaces connected to the vertex

![alt text](<sources/Shading Transformations/Screenshot 2025-12-03 at 8.09.51 PM.png>)

You first calculated the colour + shading at the vertices and then you can compute the colour for any other point as you would with barycentric coordinate

$$
C = \alpha C_0 + \beta C_1 + \gamma C_2
$$

# Phong Shading

Instead of interpolating colours, we can instead interpolate the vertex normals...

$$
n = \frac{\alpha n_0 + \beta n_1 + \gamma n_2}{|\alpha n_0 + \beta n_1 + \gamma n_2|}
$$

Then you can just stick that $n$ into one of the many shading equations.

# Shading Conventions

If you recall, there are 4 types of spaces we can work in: Model, World, View, Canonical view. In order to do shading, we should pick one space else the equation may not work out. We usually don't use canonical view space since angles there are warped, but the 3 other ones are fair game.

It is however very often done in the view space. Usually, you'll even see that the light sources are already defined in view space. This really just means that during our operations, we will need both model-view matrix and the model-view-projection matrix. The former will be use in the fragment shader (or wherever you decide to do your shading)

# Transforming Normals

There's this funny issue when you try to apply transformation on the surface normals. We know that the normals are directional vectors, and so we have to set the last entry of the homogenous coordinate to be 0, that's ok. The issue happens when you take non-uniform scale transformations.

Say we have $\bold{n} \cdot \bold{t} = 0$. If we apply non uniform scale $\bold{s}^T = [a,b,c]$ then

$$
\bold{n}^\prime = \bold{s} \cdot \bold{n}\\
\bold{t}^\prime = \bold{s} \cdot \bold{t}\\
\bold{n}^\prime \cdot \bold{t}^\prime = a^2n_1t_1 + b^2n_2t_2 + c^2n_3t_3
$$

after which, you can no longer say $\bold{n},\bold{t}$ are perpendicular anymore.

The fix to this problem is to first transform normals by the inverse of the scale matrix. You can imagine that in the expression of $\bold{n}^\prime \cdot \bold{t}^\prime$, the scale coefficient will just cancels out and the 0 is preserved.

Next is on the issue of the transformation itself. We wanna keep things uniform with matrix multiplication but seems like this won't do. Turns out however, we can do it by first using only 3x3 part of the transformation matrix (we don't need translation). Then, express it in terms of it's rotation and scale:

$$
M_{3 \times 3} = R_1SR_2
$$

We can do this by SVD decomposition. However, we'd like:

$$
M_{normal} = R_1S^{-1}R_2
$$

By some magic:

$$
M_{3 \times 3}^{-1} = R_2^{-1}S^{-1}R_1^{-1}
$$

$$
{M_{3 \times 3}^{-1}}^T = {R_2^{-1}}^TS^{-1}{R_3^{-1}}^T
$$

The rotational matrices you see are what we call orthogonal matrices. One of their nice properties is that the transpose of the matrix is equal to be their inverse i.e. $A^{-1}=A^T$. Hence, we can rewrite it into the following:

$$
{M_{3 \times 3}^{-1}}^T = R_2S^{-1}R_3
$$

And so, we have it:

$$
M_{normal} = {M_{3 \times 3}^{-1}}^T
$$

# Reference

https://www.youtube.com/watch?v=Q_TYQvZS6WE&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=18
