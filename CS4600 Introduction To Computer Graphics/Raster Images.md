# Raster Images

# Gamma

This deals with intensity of pixels. Remember that we cannot have continuous values with computers. This issue weigh heavily on how colours should be distributed particularly between darker and lighter colours. This is due to the fact that we can differentiate darker colours better than we can lighter colours. A nice example is with numbers. 1 Vs 2 has a clear difference where the right side is twice as big. But consider 1000000 Vs 1000001. The difference is the same but the latter number you would consider to be approximately the same. With this being the case, and to give much more representability, the colour value of your image will be taken to a power of $\gamma$ before being displayed.

It should be noted that the values stored in the image actually has no idea about this process. They’re just stored as is. The idea of having more representability is simply due to the fact that there will be more mapping of numbers to lower values (note that colours are in a range of [0,1]). And looking at an exponential graph:

![Screenshot 2025-09-14 at 11.29.34 PM.png](sources/Raster%20Images%2026e1aa5d8a0e80f99f72e2842289155d/Screenshot_2025-09-14_at_11.29.34_PM.png)

A higher value is actually a lower one. But, you can see that a 1 is still represents the highest value your screen can display.

Btw, this theme of keeping extreme values and interpolate the in-between is quite the theme in computer graphics

# Pixels

Let’s get straight to the point: pixels are not little squares. Pixels are points. The images that are shown through a screen may show rectangles but that is because that’s the best they can do. In reality, it looks a little more like this:

![Screenshot 2025-09-14 at 11.32.45 PM.png](sources/Raster%20Images%2026e1aa5d8a0e80f99f72e2842289155d/Screenshot_2025-09-14_at_11.32.45_PM.png)

To fill in the rest of the area, we’ll interpolate from the data that we have

# Alpha-Blending

This deals with image with transparency built in within them. You’ll probably recognise this type of features before if you use files with a .png as an extension. This leads to the RGBA image format which basically gives 4 channels: Red, Green, Blue, and opacity (Alpha) channels. When a pixel’s alpha is 1 it’s whole colour is displayed perfectly while if alpha = 0, the pixel is black no matter the values in the RGB channels; we just multiply alpha to the RGB values.

One question to consider is with what if we have 2 images both of which having an alpha channel? Well, some of the effects that we’d love to have would be to say if the foreground image has an opacity of 0, then we should see the background pixel clearly (and of course if the foreground has an opacity of 1, we won’t see the background).

Let’s first deal with the alpha values. Given the requirement, we can come up with following:

$$
\alpha=\alpha_{f} + (1-\alpha_{f})\alpha_{b}\tag{1}
$$

This will say that if any of the alpha is 1, there **will** be something shown in that pixel.

Now let’s think about how we can use this opacity values; what should the colour be? Well, following the requirement, we can come up with something similar:

$$
c=\alpha_{f}c_{f}+(1-\alpha_{f})c_{b}\tag{2}
$$

But this won’t work as nice if background pixel also has opacity (since it never popped up in the equation). The extra case that we want is that if the background opacity is < 1, and the foreground opacity is also less than 1, then we would like to see the background colour to be even more transparent. To count for it, a possibility would be:

$$
c=\alpha_{f}c_{f}+(1-\alpha_{f})\alpha_{b}c_{b}\tag{3}
$$

And in fact, since alpha is involved in both side of the calculation, it only make sense to say (3) is the colour with opacity taken into account. This leads us to the final alpha-blending equation

$$
c=\frac{\alpha_{f}c_{f}+(1-\alpha_{f})\alpha_{b}c_{b}}{\alpha}
$$

Where the denominator alpha comes from eq. 1

Keep in mind that these things are quite arbitrarily decided. You’ll find that there are actually many other kinds of blending following some other types of rules and they all give different effects.

# References

https://www.youtube.com/watch?v=PMIPC78Ybts&list=PLplnkTzzqsZTfYh4UbhLGpI5kGd5oW_Hh&index=5