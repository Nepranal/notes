# Depth Of Field

Depth of field is defined as the range of distance at which the object that lies on a plane on those distances will be sufficiently focused e.g. Have a blur circle less than the pixel size.

Recall that a lens has only a single plane of focus because at any other distance, the point will form a blur circle at the image plan. What's interesting here is that you don't necessarily need to have something to be sharp at a point so long as the blur cirlce is not as big as a 'pixel'.

A pixel is usually approximated with a certain size. So, if your image viewer is let's say a computer screen, so long as the captured blur circle is less than the pixel, then you won't even notice the blur (since the thing has to be blurred to begin with)

> idk if this is true, but seems like everything has a pixel (even the silver halide film)

# Calculating DoF

![alt text](<sources/Depth of Field/Screenshot 2026-01-05 at 1.35.25 AM.png>)

The goal is simple: calculate $o_2 - o_1$.

Given that we know $b = c$, from our formula for blur circles

$$
c = \frac{f^2}{N}\frac{(o_2-o)}{o_2|(o-f)|}
$$

We can rewrite this as:

$$
c = k\frac{(o_2-o)}{o_2}
$$

where $k = \frac{f^2}{N(o-f)}$. Which then

$$
o_2c = o_2k-ok\\
o_2 = \frac{ok}{k-c}
$$

Similarly, we can rewrite $o_1$ as

$$
o_1 = \frac{ok}{k+c}
$$

Where $k$ is defined similarly. Then,

$$
o_2-o_1 = ok(\frac{1}{k-c}-\frac{1}{k+c}) = ok\frac{k+c-k+c}{k^2-c^2}
$$

$$
=ok\frac{2c}{k^2-c^2} = \frac{2ocN^2(o-f)^2}{f^4-c^2N^2(o-f)^2}\frac{f^2}{N(o-f)}
$$

$$
o_2 - o_1= \frac{2ocf^2N(o-f)}{f^4-c^2N^2(o-f)^2}
$$

# Hyperfocal Distance

Every lens has what's called a hyperfocal distance. It's the distance at which if you focus the lens there, any image from that point to infinity will always be in focus.

![alt text](<sources/Depth of Field/Screenshot 2026-01-05 at 2.00.04 AM.png>)

This is the following situation where we are asking, what would happen as $o_2$ grows and we want to fix $b = c$:

$$
c = k\lim_{o_2->\infty}\frac{o_2-o}{o_2} = k\lim_{o_2->\infty}1 - \frac{o}{o_2} = k
$$

$$
c = \frac{f^2}{N(o-f)}
$$

$$
o = h = \frac{f^2}{Nc} + f
$$

So, if the lens is focused at $h$, any point further than it will always be in focus.

# Tissue Box Camera Experiment

Let's consider an experiment. Say I have a camera with a lens on it. What I will do is that I will put a tape over the lens. What do you think will happen? Will the image be blocked?

Turns out, it won't. Remember that the idea of the lense is to allow more light to come in. So, even though you block it, you're really only blocking some light to come in from that point. As a result, we still see an image but it does indeed gets darker.

# Lens Related Issues

## Vignetting

Typically, a camera comes in multiple lenses. This give more capabilities for the lense by dealing with the defect of a lense (e.g. Making sure the captured image is focused everywhere). An issue however is that the imaging plane is a little further down of which the incoming light may not be able to hit. Typically, its related to the smallest aperpture of the lenses

This makes some spots on the camera darker around the edges.

## Chromatic Abberation

This happens because light, white light, is composed of many other visible light which refracts differently as they pass a lense. This gives the image some random colours since the coloured light components is not reflected at different angles.

## Geometric Distortion

Distance between points in the scene may also a mess depending on the lense quality. Sometimes some points are further than what they should be causing something called radial distortion or tangential distortion. This si caused by lense imperfection

Although, this geometric distortion could be useful if we know how much the distortion is.

# Reference

1. https://www.youtube.com/watch?v=v5OE90eVIXo&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=5
2. https://www.youtube.com/watch?v=hzOeqCb2Fg4&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=6
