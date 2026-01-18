# Lenses

Lenses comes in to help us solve the exposure problems that pinhole has. A lense can gather more light from a point in your scene and focus them to a point in the image plane.

![alt text](<sources/Image Formation using Lenses/Screenshot 2026-01-05 at 12.00.35 AM.png>)

The strength of the lense is determined by its focal length. Though, unlike before, the focal length for the lense is not the distance between the lense and the image. Instead, it's more of an intrinsic property of the lense. It also has the following property (Gauss Lens (thin lens) Law):

$$
\frac{1}{i} + \frac{1}{o} = \frac{1}{f}
$$

where $i$ is the distance from image to lens and $o$ is the same thing but from the actual point.

## Finding The Focal Length

Although the focal length is intrinsic, we can actually carry out an experiment to find $f$. The idea is to use an object far away that behaves like a point, say something like the sun.

We can put a lens in the direction of the sun rays and put a paper before the lens. We'll vary the length of the lense until a sharp (sharp enough) image is formed at the paper (or really, any background that can form an image).

Since the sun is far away and behaves kinda like a point at a faraway distance, we can take that $1/o \approx 0$. This leaves $i = f$. $i$ is the distance between your lense and the formed image (ideally close enough to a point).

# Lense Magnification

To treat magnification, we'll do this per dimension

![alt text](<sources/Image Formation using Lenses/Screenshot 2026-01-05 at 12.13.24 AM.png>)

Magnification is defined as

$$m = \frac{h_i}{h_o}$$

By similar triangles, we can say that $m = i/o$.

## Magnification With Multiple Lenses

We can play around with this magnification by setting up multiple lenses

![alt text](<sources/Image Formation using Lenses/Screenshot 2026-01-05 at 12.15.56 AM.png>)

The intermediate range will have $m' = \frac{h^{'}_i}{h_o} = \frac{i_1}{o_1}$. At the actual image, we will have $m = \frac{h_i}{h_i^{'}} = \frac{i_2}{o_2}$. So the overall magnification will actually be

$$
\frac{h_i}{h_o} = m \times m' = \frac{i_2}{o_2}\frac{i_1}{o_1}
$$

or more generally

$$
\prod_j \frac{i_j}{o_j}
$$

Also, with the two lenses system, you can move the lenses around to achieve a zooming effect.

# Aperture

Cameras with a lense typically come with an arperture where it can control how much light comes in. With these arpertures, it usually comes with a number that defines the diameter of the exposed lens called the f-number.

$$
N = \frac{f}{D}
$$

where $N$ is the f-number, $f$ is the focal length, and $D$ is the diameter.

# Lens Defocus

The bad thing with lenses, is that they only have a single plane of focus (although, later we'll see this can be handled). As you move the object around i.e. as you change $o$, you'll have to move $i$ so that you'll get a focused image.

![alt text](<sources/Image Formation using Lenses/Screenshot 2026-01-05 at 12.41.36 AM.png>)

But obviously, you can't freely move $i$ all the time. What will happen is that you will get blur circles. So, instead of having your image formed at a point, it will instead form a region at the image plane.

For this particular case, we can compute the blur by similar triangles

$$
\frac{b}{D} = \frac{|i'-i|}{i'}
$$

As you see, the blur circles depends on the aperture's diameter. The bigger it is, the bigger the blur you'll get

By Gauss's lens law, we know that:

$$
i = \frac{of}{o-f}
$$

Which means

$$
i - i' = \frac{of}{o-f} - \frac{o'f}{o'-f} = \frac{f^2(o-o')}{(o-f)(o'-f)}
$$

We can put this all together to get an expression for the blur circle

$$
\frac{b}{D} = \frac{f^2}{i'}\left|\frac{(o-o')}{(o-f)(o'-f)}\right|
$$

One thing to note here is that $i$ should be a positive number

$$
b = \frac{|o-f|f^2}{o'f}\left|\frac{(o-o')}{(o-f)(o'-f)}\right| = D\frac{f}{o'}\left|\frac{(o-o')}{o'(o-f)}\right|
$$

Finally, expressing everything in terms of the f-number:

$$
b = \frac{f^2}{N}\left|\frac{(o-o')}{o'(o-f)}\right|
$$

# References

1. https://www.youtube.com/watch?v=7LX-19v_9ns&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=3
