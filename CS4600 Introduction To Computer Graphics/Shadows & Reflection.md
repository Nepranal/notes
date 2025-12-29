# Bias

A simple way to check for shadows is to first shoot a ray (primary ray) from your pixel to the point of interest and then check if you can reach, from that point, a light source by a straight ray. If you can, then the point won't be a shadow. This second ray is called a shadow ray, a type of secondary rays.

If you just implement this naively, what you'll find is a rather weird result. Indeed, you'll find that areas which are ocluded will be a shadow (black) but open areas would also have some shadows on them even though they're not occluded at all. What happened?

![alt text](<sources/Shadows & Reflection/Screenshot 2025-12-19 at 10.12.34 PM.png>)

The issue is with computational inaccuracies e.g. Rounding up/down. When you shot a ray, you'll have to find where that ray intersects with the primitive. This intersection point may not be the vertices themselves so you'll have to perform some computations with real numbers which you will have to inevitably take an approximation of.

When you take this approximation, your point won't exactly be on the intersection point but can rather be on top or below the actual point. When you're on top, you'll be fine (for the most part, i.e. There's no other object super close to you) since you're supposed to ignore that plane anyways. The issue happens when you're below the plane. As you try to see if you can reach a light source, you'll find that you got blocked by yourself.

![alt text](<sources/Shadows & Reflection/Screenshot 2025-12-19 at 10.16.50 PM.png>)

There are actually some interesting solutions to this problem. The first and the simplest one is to add a constant term called a bias. So, you'll have

$$
\bold{x} = \bold{p} + (t + \delta)\bold{d}
$$

where $\delta$ is the bias term. This will effectively move your point a little bit in direction $\bold{d}$ and hopefully get the first case where your point is on top of the surface. Algorithmically, you can say

```
if ray hit:
    if t > bias:
        return true
```

On the value of $\delta$, you have to be a little careful. Too big, and you may have a false negative. Typically you'd adjust it with the scale of your scene. But, it's usually a small number (again, relative to scale).

# Shadow Mapping

This is another solution to our rounding off problem. We'll generate a depth map of the scene from the light source's perspective. The depth map is an actual raster image rendered by presumably a rasterizer.

![alt text](<sources/Shadows & Reflection/Screenshot 2025-12-19 at 10.35.46 PM.png>)

You'd do this for every depthmap. So, when we want to know for a particular point wheter it will be a shadow or not, we will go through each depth map of the light source and see if our point can be seen presumably by checking the depth at each pixel of the depth map.

# Perfect Specular Reflections Of Objects

Here we'll talk only about perfect reflections i.e. We're only considering lights coming from objects at angle equal to the viewing angle. The addition is actually a quite straightforward modification of the rendering equation

$$
L(\omega_o) = \int_\Omega L_i(\omega_i)cos(\theta_i)f_r(\omega_i, \omega_o)d\omega_i + L_r(\omega_r)K_r
$$

![alt text](<sources/Shadows & Reflection/Screenshot 2025-12-19 at 11.29.00 PM.png>)

The extra term of $L_r(\omega_r)K_r$ would give the specular reflection from other objects coming from the perfect reflection angle. $K_r$ is the reflective constant but we usually just use $K_s$; the specular constant.

$L_r(\omega_r)$ is the incoming light ray from the other object (if any). In order to compute it, we actually kinda have to solve a recursive problem. To figure it out, we can shoot a ray at that reflection direction and see if we hit anything. If we hit, we would shade that point to get $L_r(\omega_r)$.

The issue is that, that point may also undergoes perfect reflection. So we have to consider the same problem again. That is, before returning the current $L_r(\omega_r)$ we must do the same operation again until we either hit the light source itself or a surface that does not undergoes perfect reflection.

![alt text](<sources/Shadows & Reflection/Screenshot 2025-12-19 at 11.37.47 PM.png>)

This can goes on indefinitely and at times we can't really see the difference. So typically we'd limit this by something called a bounce limit

# Reference

https://www.youtube.com/watch?v=l_iVdRbA_4s&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=21
