# Textures

# Representing Textures

To think about this, think about what textures are supposed to do. Put it simply, there are there to store colour information at each vertex. So you can imagine that perhaps, you can store it as an image; indeed you can. More than that, you can also store textures as a 1D selection of colours, and a 3D block

![Screenshot 2025-11-02 at 8.48.30 PM.png](sources/Textures%2029a1aa5d8a0e808fa67ff228c398cac7/Screenshot_2025-11-02_at_8.48.30_PM.png)

Additionally, you can also store procedural textures where you query the texture colours from a function. Quite popular these days with NeRFs (Neural Radiance Field) and all

# uv Vs st coordinates

Now, say we have an image to be used as our texture. The next thing you have to do is to assign where in the texture, the associated vertex is supposed to be. The texture itself has a coordinate system called uv-coordinates. It’s nothing fancy, just imagine your typical cartesian coordinate system. Something to be aware here though is that there’s another one called st-coordinates.

![Screenshot 2025-11-02 at 8.52.11 PM.png](sources/Textures%2029a1aa5d8a0e808fa67ff228c398cac7/Screenshot_2025-11-02_at_8.52.11_PM.png)

Luckily, they don’t have much of a difference, is just that st-coordinates are normalized. Though, It is noted that st-coordinate is more popular than uv-coordinates because of the flexibility they provide. Imagine if you have a higher resolution texture. With st-coordinate, you don’t have to reassign the vertices again if you want to replace the underlying texture

# Bilinear Filtering

Filtering means sampling. We know how textures are stored, and perhaps it does kind of make sense to imagine textures to be stored like an image. Unfortunately, similar to an image, we can't really think of textures just as a bunch of coloured squares. Rather, we need to think of it as a bunch of coloured dots just like with images. Instead of a pixel, we have a texel:

![alt text](<sources/Textures 29a1aa5d8a0e808fa67ff228c398cac7/Screenshot 2025-11-28 at 7.01.14 PM.png>)

How can we extrapolate data? Texels are specified to a point, but obviously, the point we assign to our shape may not be directly on a texel. How can we get the colour then? Bilinear filtering is one way to do it by just simply doing linear interpolation twice (hence, bilinear)

![alt text](<sources/Textures 29a1aa5d8a0e808fa67ff228c398cac7/Screenshot 2025-11-28 at 7.06.25 PM.png>)

Bilinear looks at the closest neighbours of our point and doing interpolation from one dimension to the next. In the figure, we first figure out $c_{01}$ & $c_{23}$. Then, using those, we do one more interpolation to get $c$.

$$
c_{01} = (1-s)c_0 + sc_1\\
c_{23} = (1-s)c_2 + sc_3\\
c = (1-t)c_{01}+tc_{23}
$$

and, good news, the result will always be the same from which dimension you start. Really, bilinear filtering is quite similar to barycentric coordinate where we use all 4 points (instead of 3) and the area of the rectangle (instead of triangle) opposite of the point is the weight:

$$c=stc_3 + (1-s)tc_2 + (1-t)sc_1 + (1-t)(1-s)c_0$$

Keep in mind: this is just one way to do it. Interpolation is just guessing what the colour could be from the information we have in a rationale way.

# Mipmaps & Trilinear Filtering

The methods we have discussed so far, or it's variation, may suffer as the camera moves. This happens especially when an object is far away and the size of the texels are way smaller than the pixels. When this happens, you'll suffer from flickering:

![alt text](<sources/Textures 29a1aa5d8a0e808fa67ff228c398cac7/nomipsshort.gif>)

The problem is that when you just move a little, and the object is far away, the region of the texture you're trying to sample also varies dramatically leading to flickering.

What we can do here is, instead of having exactly a colour at a point, is to have a representative colour over region of the texture that's going to be drawn for our pixel. This can be achieved by, for instance, taking an average colour over that region.

This is fine but if you have a very high resolution texture, taking an average can be very expensive to do. The solution for this is called pre-filtering, and mipmap is a way to do pre-filtering.

With Mipmapping, we won't be directly taking average but we will still have a representative colour. First, we'll have different versions of our texture at gradually decreasing resolution

![alt text](<sources/Textures 29a1aa5d8a0e808fa67ff228c398cac7/Screenshot 2025-11-29 at 6.29.35 PM.png>)
Each level you can imagine as having a bigger pixel size.

When we want to render, we first figure out which level has a texel size that's comparable to the pixel size of the thing to be rendered. Once you figure this out, just do as you would normally e.g. Taking bilinear filtering.

Though, we can do a little better than this. In the event that your pixel size is in between 2 different levels, you can interpolate from both levels and do a final interpolation between those 2 colours. We call this trilinear filtering.

There's something else we can put on top of mipmapping. You see, mipmaps would approximate the region for the pixel. This would create blurred images at far distances which is okay, but they can sometimes be too much
![alt text](<sources/Textures 29a1aa5d8a0e808fa67ff228c398cac7/Screenshot 2025-11-29 at 6.44.53 PM.png>)

We can improve this by doing something called Anisotropic filtering. Not too worry, it just means we're going to better approximate our region to the pixel size. We can still do rectangular shapes but we do sample it multiple times and aggregate them together

![alt text](<sources/Textures 29a1aa5d8a0e808fa67ff228c398cac7/Screenshot 2025-11-29 at 6.47.05 PM.png>)

# References

https://www.youtube.com/watch?v=Yjv6hc4Zqjk&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=15&t=10s
