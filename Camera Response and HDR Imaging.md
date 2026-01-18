# The Camera Response Function

In a way, this talks about how intensity of the scene and the pixel intensity typically doesn't have a linear relationship. This is due to some facts like our eyes are more sensitive to certain interval of brightness or due to pixel qualities.

The brightness of a pixel, can be computed as follows

![alt text](<sources/Camera Response and HDR Imaging/Screenshot 2026-01-11 at 8.42.30 PM.png>)

$$
B = I\frac{\pi d^2}{4}t
$$

where $B$ is brightness, $\frac{\pi d^2}{4}$ is the lens' aperture and $t$ is the integration time (exposure). After this stage, $B$ is passed on to another piece of electronic producing $M(B)$, the measured brightness, where the pixel brightness could no longer be linear to actual brightness.

## Finding The Camera Response Function

The main idea is to take a samples from the camera itself and generate the gamma curve of the camera. We can provide a surface with multiple colours for which we know exactly how they would reflect light e.g. Their reflectance rate.

Then, we shine the same amount of light for each of them and take a picture with our camera and plot the the brightness captured and plot them against what we were expecting. Finally, you can connect these points into a curve that best approximate them.

> I should note here that after this whole process you'll make the curve
>
> ![alt text](<sources/Camera Response and HDR Imaging/Screenshot 2026-01-11 at 8.58.24 PM.png>)
>
> As you try to use the curve and interpolate, the $B$ that you'll get won't be the actual scene brightness but will be proportional to it. I find it kind of amusing since the actual graph is created using the actual brightness coming from the scene. I guess it's because we're just guessing the line between points

# High Dynamic Range

From what we know so far, if you take a picture of a scene, and if that scene has a very diverse degree of lighting, then it could be very difficult to adjust exposure in order to get good details on all of the different regions due to limited dynamic range. How can we fix this?

The idea is to take the picture of the image multiple times at different exposures, each with a different camera response function (caused by different exposure), and add them together.

![alt text](<sources/Camera Response and HDR Imaging/Screenshot 2026-01-11 at 9.09.22 PM.png>)

The resulting camera response also changes. It would be as if you had a virtual camera that took a picture of that very scene instead. In a way, this allows your image pixels to have a higher dynamic range. Here, at most you can have a maximum brightness of $255 \times 4 = 1020$ with a minimum of $0$.

$$
M_{HDR} = M_0 + M_1 + M_2 + M_3
$$

![alt text](<sources/Camera Response and HDR Imaging/Screenshot 2026-01-12 at 12.40.00 AM.png>)

Here you see that we can increase the exposure for points at the scene with lower brightness and those that has a higher one won't neccessarily becomes super bright since each image is capped at $255$.

The images can then be further processed to produce an image that has considerably more details than each individual image

> I'd imagine the little bit of processing is just to normalise the values to be displayed.

# Single-Shot HDR

We can push this a little further. In order to get a new camera response function, we need a different exposures. So, what we can do is to adjust the pixels a little bit so they individually have different exposures. You can then aggregate, by looking at neighboring pixels, and follow the same reasoning as before.

# References

1. https://www.youtube.com/watch?v=95DNdbxaIXE&list=PL2zRqk16wsdr9X5rgF-d0pkzPdkHZ4KiT&index=15
