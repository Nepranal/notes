# Ray Intersections

This section is about how you would compute intersection between a ray with a bunch of different surfaces. We represent a ray with

$$
\bold{x} = \bold{p} + t\bold{d}
$$

where $\bold{p}$ is the origin of the ray and $\bold{d}$ is the direction. Typically, you'll have $|d| = 1$.

## Sphere Intersection

A sphere:

$$
a^2 + b^2 + c^2 - z^2 = 0\\
\bold{x} \cdot \bold{x} - z^2 = 0
$$

This is a centered sphere at origin. The equation is basically saying 'get all points with a length of $z$ from the origin'. A more general one would be:

$$
(\bold{x}-d) \cdot (\bold{x}-d) - z^2 = 0
$$

To get the intersection, we can just plug our equation in:

$$
(\bold{p} + t\bold{d}) \cdot (\bold{p} + t\bold{d}) - z^2 = 0
$$

This is ends being a quadratic equation if you expand the terms.

To use the solutions of the equation: The rays, primary rays, comes off from the screen and potentially hits the sphere. The rays themselves should always be put infront of $\bold{p}$ i.e. $t$ should always be bigger than 0.

It could also hit the sphere both times, once to enter and once to leave in which case, there will be 2 $t$. Since we only care about the first time we hit, we'll take the smaller $t$. That is, we'll always take this solution to the quadratic equation:

$$
t = \frac{-b - \sqrt{b^2-4ac}}{2a}
$$

It would be very nice to compute the determinant first to make sure it's bigger than 0 (geometrically, we don't really care if $t$ is imaginary since it's kinda hard to make sense and will lead to a miss physically).

## Plane Intersection

A plane can be written down as:

$$
\bold{x}\cdot \bold{n}+d = 0
$$

By just plugging things in, we can solve for $t$:

$$
t = -\frac{\bold{p} \cdot \bold{n} + d}{\bold{d}\cdot \bold{n}}
$$

As you'd expect, you would have to be careful for when the line is parallel with the plane i.e. $\bold{d} \cdot \bold{n} = 0$.

> This is not talked in the reference, but I suppose you'll also have to be careful when there are infinitely many solutions. I'm guessing in this case, you can just return $\bold{p}$

## Triangle Intersection

There are many ways you can do this. A nice one is to extend the triangle into a plane and check if the intersection point satisfies the barycentric coordanate requirement that we have i.e. All the coefficients of the vertices to make up that point be between 0 and 1.

# Accelerating Ray Tracing: Axis-Aligned Bounding Boxes

Implement it naively, and ray tracing can get super slow. You have to:

```
closest = +inf
for each pixel:
    for each ray:
        for each primitive:
            if intersect:
                if location of intersect < closest:
                    closest = location of intersect
return closest
```

When you have millions and millions of object of high quality, and your screen has a very high definition e.g. 4K, your stuff just won't get done in reasonable time even if you're doing offline rendering.

Though, one thing that we can work from is that these rays are very 'thin' and that we really only care about the first thing we hit. So, the idea is to create data structures that would allow you to immediately decide whether or not to look into a particular set of primitives.

One of those structures are called Axis-Aligned Bounded Boxes. Axis-Aligned just mean that the planes of the side of the boxes are aligned with the planes of the coordinate system. What you'd do is you put your scenes inside of boxes and only check the primitives within the bound box if your ray intersects with the box

![alt text](<sources/Ray Tracing/Screenshot 2025-12-16 at 12.05.13 AM.png>)

## Bounding Volume Hierarchy

We can actually take this idea further. Notice that we can only really benefit from this if there are not a lot of primitives within the box or if the ray doesn't hit the box. Drawing a balance would be quite difficult

So, why don't we just make boxes within boxes. That is, if your ray intersect with a box, it will check further which boxes within that box has been intersected with:

![alt text](<sources/Ray Tracing/Screenshot 2025-12-16 at 12.08.02 AM.png>)

Kinda like a search tree (note: doesn't have to be binary). The object doesn't need an individual box as well. You can split an object further into it's primitive such that the leaf boxes would contain only 4 or 5 primitives.

# Reference

1. https://www.youtube.com/watch?v=gGKup9tUSrU&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=20
