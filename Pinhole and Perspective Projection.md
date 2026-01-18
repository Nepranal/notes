# Pinhole Cameras

What we understand of light is that we can see things (shapes, colours, etc.) because of it. But then, how come when you hold a white piece of paper, no image ever shows up even though light, the same ones that go to your eyes, hit it? The idea is that there is indeed an image forming. But, because light comes from every direction, to every point on your surface, the image becomes distorted. So, if we can restrict the light, then perhaps there will be an image forming.

This is the idea of the pinhole. We'll restrict the incoming light by having a small hole where ideally it is a 'point' so that from a certain particular point, the light bouncing off of that point will land on the image trough a single point i.e. The pinhole.

Now, let's describe how the image will form. This is our model:

![alt text](<sources/Pinhole and Perspective Projection/Screenshot 2026-01-04 at 7.08.34 PM.png>)

Give a point $P_o$, it will create an image at a point $P_i$ at the image plane. Between $P_o$ and $P_i$, we can form similar triangles (the dotted line from the optical axis to either forms a $90\degree$ angle). With this being the case, we can write that

$$
\frac{x_o}{z_o} = \frac{x_i}{z_i}\\
$$

We can do this for $y_i$ and overall get that

$$
\begin{pmatrix}
x_i \\
y_i
\end{pmatrix} = \frac{z_i}{z_o} \begin{pmatrix}
x_0 \\
y_0
\end{pmatrix}
$$

We usually call $z_i$ as $f$, the effective focal length. Also, this kind of projection is called perspective projection.

# Line Projection

How would a line be projected under our pinhole model?

![alt text](<sources/Pinhole and Perspective Projection/Screenshot 2026-01-04 at 7.24.18 PM.png>)

A line and a point in 3D space produces a plane. Every ray that comes from the line and goes through the pinhole must lie in the plane as the ray must intersect both the point at the line and the pinhole.

Since every ray from the line is in the plane, the projection of the line in the image plane must be a portion of the intersection between the plane and the image plane. With this being the case, the projection must also be a straight line as the intersection of two planes must be a straight line.

# Magnification

So it turns out, as an object gets further from the pinhole, it will get smaller in size

![alt text](<sources/Pinhole and Perspective Projection/Screenshot 2026-01-04 at 7.51.52 PM.png>)

The projection of $A_o$ and $B_o$ will be scaled by $\frac{f}{z_o}$ where $f$ is the focal length and $z_o$ is the distance of the object. As $z_o$ gets bigger, that fraction gets smaller.

We define magnification as follows:

$$
||m|| = \frac{||d_i||}{||d_o||} = \frac{\sqrt{\delta x_i^2 + \delta y_i^2}}{\sqrt{\delta x_o^2 + \delta y_o^2}}
$$

$d_i$ = distance in image plane. By transforming the points, we can find a nicer form

$$
x_i^A = \frac{f}{z_o}x_o
$$

$$
x_i^B = \frac{f}{z_o}(x_o + \delta x_o)
$$

we can then get

$$
x_i^B - x_i^A = \delta x_i = \frac{f}{z_o}\delta x_o
$$

of which we can get the same expression for $\delta y_i$

$$
||m|| = \frac{\sqrt{(\frac{f}{z_o})^2(\delta x_o^2 + \delta y_i^2)}}{\sqrt{\delta x_o^2 + \delta y_o^2}}
$$

By some further manipulation, we can get that

$$
||m|| = ||\frac{f}{z_o}||
$$

> If the object you're looking at is small compared to the distance from your camera, then the magnification throughout the object can be considered to be the same. I'd imagine it's because $z_o$ will quite similar troughout the surface of the object

## Vanishing Point

The effect of magnification has an interesting effect on parallel straight lines. Particulalry, all parallel lines will seem to be touching at a point at infinity called the vanishing point. This is because, although parallel lines don't meet and they have a constant distance between one another, this projected distance must get smaller as the lines go further and further.

We can even determine where this point is in our image by measuring the parallel lines w.r.t the camera. First, we draw a ray that starts at the pinhole and is parallel to the other parallel lines in the scene. Since the ray is parallel, it must also go to the vanishing point as the other parallel lines as seen from the image plane. Since the projection of this line is a point, that point must be the vanishing point.

> I think it's important to note here that the vanishing point doesn't actually exist in the scene. It exists in the image due to perspective projection

![alt text](<sources/Pinhole and Perspective Projection/Screenshot 2026-01-04 at 11.38.27 PM.png>)

If you can measure direction of the parallel lines w.r.t to the pinhole, say $l = (l_x, l_y, l_z)$, then you can create a vector from the pinhole with the same direction as $l$ and apply the projection as formulated before. The vector can be of any length as only the direction is important.

# Pinhole Problems

The pinhole camera is not without problems however. The size of the pinhole is one thing. It can neither be too big or too small. If it's too big, then blurring will happen. If it's too small, then diffraction of light will happen. Which will also lead to blurring.

Another issue is with lighting. Since the pinhole has to be somewhat small, the amount of light it can gather (the photons) will also be reduced. A pinhole camera requires an exposure time; an interval in which the camera is to be held steady so that it can capture enough light. Of course, the scene itself should also be still.

# References

1. https://www.youtube.com/watch?v=_EhY31MSbNM&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=2
