# Capturing Lights From Other Objects

In the actual world, lights come from many different surfaces and not only from the light source. We can make this more explicit by modifying the rendering equation and splitting incoming lights into 2 components: direct and indirect

$$
L(\omega_o) = \int_\Omega (L_i^{direct}(\omega_i) + L_i^{indirect}(\omega_i))\cos(\theta_i)f_r(\omega_i, \omega_o)d\omega_i \\
= \int_\Omega L_i^{direct}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o)d\omega_i + \int_\Omega L_i^{indirect}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o)d\omega_i
$$

indirect being light reflected off of a surface.

And typically, since we usually have a limited direct light, you'll find the equation to be:

$$
L(\omega_o)= \sum_{i=1}^N L_i^{direct}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o)  + \int_\Omega L_i^{indirect}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o)d\omega_i
$$

The question now is how can you deal with the right side of the equation?

# Whitted-Style Ray Tracing

Whitted-Style basically says that your surface will give out any reflection it can from objects.

We can do this by first considering Phong/Blinn shading. Since we will give perfect reflection, we'll have $\alpha = \infin$ and that $K_d = 0$ since we won't have any light being diffused. This will give out

$$
f_r^{indirect}(\omega_i, \omega_o) = K_s\frac{\cos(\phi)^\infin}{\cos(\theta)}
$$

And, since $0 \le \cos(\phi) \le 1$, we can rewrite it as

$$
f_r(\omega_i, \omega_o) =
\begin{cases}
    \frac{K_s}{\cos(\theta)} & \omega_o = \omega_i \\
    0 & otherwise
\end{cases}
$$

This would lead the function under the integral to evaluate to 0 on every direction except on 1 angle that is the viewing angle $\omega_o$. This will simplify the infinite sum to just be:

$$
L_i^{indirect}(\omega_r)cos(\theta_r)f_r(\omega_r, \omega_o)
$$

where $\omega_o=\omega_i=\omega_r$ and $\omega_r$ is the perfect reflection of $\omega_o$.

We can further simplify this to be:

$$
L_i(\omega_r)cos(\theta_r)(\frac{K_s}{\cos(\theta_r)}) = L_i(\omega_r)K_s
$$

On the whole, the equation will become:

$$
L(\omega_o)= \sum_{i=1}^N L_i^{direct}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o) + L_i^{indirect}(\omega_r)K_s
$$

> If you follow the lecture series, you'll find that this equation is exactly the same as in the 'Shadows & Reflection' lecture where we consider only perfect reflections of objects. I suppose this is the justification of that model in a way and why we can simply take the product of the incoming light with the specular constant of the surface. It's just a simplification of the BRDF in the case of indirect illumination.

# Monte Carlo Sampling

Let's consider the problem of colouring an impartially filled rectangle. Consider the following

![alt text](<sources/Sampling/Screenshot 2025-12-21 at 11.37.16 PM.png>)

How can we pick a representative colour for this pixel? A nice way to do it is by considering the proportion of the colours within that pixel.

If we have a function $f(x)$ that gives the colour for any point within that pixel, then the representative colour could be calculated by

$$
c = \int_A f(x)dx
$$

> Check Riemann Sums where $\Delta x = \frac{1}{N}$

The issue with this integral is that it is quite difficult to evaluate. So, what we can do instead is to randomly sample some points within the area and take an average:

$$
c \approx \frac{1}{N}\sum_{i=1}^N f(x_i)
$$

As we take the limit of N, you'll get the integral back.

# Monte Carlo Ray Tracing

This time, to approximate, instead of considering every single direction, we'll just randomly sample some directions and approximate the integral as an average

$$
L(\omega_o)= \sum_{i=1}^N L_i^{direct}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o)  + \frac{1}{N}\sum_{i=1}^N L_i^{indirect}(\omega_i)\cos(\theta_i)f_r(\omega_i, \omega_o)
$$

Doing this opens up to you more kinds of reflections, i.e. imperfect reflections, for more realism.

# Path Tracing

There's an issue with the previous method: It's expensive. For each ray you shoot, you will spawn $N$ rays. With bounce limit of 1, you'll have to process $N^2$. With limit of 2, you'll get $N^3$ and so on. Your complexity would be exponential

The good news is, there is a nicer way we can do this operation. First, we set $N = 1$ still with a randomly generated direction. This path will still trace out a path that light will follow trough until the bounce limit is hit or the journey's finished

![alt text](<sources/Sampling/Screenshot 2025-12-22 at 12.38.20 AM.png>)

But, we don't stop with just 1 sample point from the pixel. We'd do this for a big $N$ at random locations within the pixel. Each colour of the pixel contributes to the overall colour of the pixel (I'd imagine with the same method of approximating the integral).

But how does this help? Well, the reason we want a big $N$ at the point itself is so that we can get as many directions as we can. After which, we can just send the shading produced by those colours into the pixel. However, notice that we don't actually need to know what the actual colour is at that point. We only want to know what the pixel would be coloured as. So, if we can simulate the same thing, we'd be fine.

Tracing all possible angles would produce a hemisphere on top of a, usually, flat surface. If you shoot enough rays, you can sort of see that that hemisphere can also be filled up in the same way.

![alt text](<sources/Sampling/Screenshot 2025-12-22 at 12.49.49 AM.png>)

Doing it this way allows us to have a bunch of samples but not having an exponentially increasing rays to process. We simply just sample more at the pixel itself.

> Isn't it weird that this same mechanism is not repeated at the reflected point (e.g. the point hit by the primary ray or the points after the bounces)? To get $L(\omega_o)$ at the next bounce point, accurately, wouldn't we need to do the same operation so we can get the proper shading?
>
> I suppose the idea is that eventually with enough samples, there will be enough points nearby to also trace out a hemisphere at the next bounce point (idk, I'm just guessing).

# Reference

1. https://www.youtube.com/watch?v=qgdDu-K0pZ4&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=23
2. https://en.wikipedia.org/wiki/Riemann_sum
