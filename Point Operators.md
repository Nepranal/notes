# Pixel Transforms

We denote transformations as follows:

$$
g(x) = h(f(x))
$$

where $g$ is the transformed image, $h$ is the transform and $f$ is the image with $x$ used to specify points of the image. With a 2D discrete image, we'll have

$$
g(i,j) = h(f(i,j))
$$

Thinking of them as simple functions, we can do some typical operations such as:

$$
g(x) = af(x) + b
$$

where $a$ and $b$ are called gain and bias respectively.

# Transforming Colors

Unfortunately, to properly transform colors within an image, more care needs to be taken. For instance, simply adding values to each pixel to an image may cause some artifacts to form. In the case of dealing with colors, simply adding values would also affect the image's hue and saturation

An approach would be to consider other color spaces that doesn't change hue and saturation during transformations. Some examples include the chromatic color space.

Essentially, you may need to convert to some space, manipulate, then convert back to RGB in order to get desired effects

# Compositing And Matting

Compositing is the act of cutting a foreground image from a background image whilst compositing means to join those images together.

This computation process uses a 4th alpha channel describing opacity. Particularly, as an object is matted from an image, we produce something called an alpha-matted image. Within this image, we use alpha channels to especially help with the edges of the object where it varies from values 0 to 1 smoothly (with of course, the main regions of the object having $\alpha=1$)

Compositing the object requires us to blend the images together and such an operation can be described with what's called the over-operator

$$
C = (1-\alpha)B + \alpha F
$$

where $C$ is the resulting composited image with $B,F$ to be background and foreground images respectively and $\alpha\isin[0,1]$. $\alpha F$ is what we call the pre-multiplied form and usually we just keep this directly

# Histogram Equalization

How can we describe the distribution of colors in an image? One way is to plot a histogram for each of the channels within the image. The histogram will act as a bucket for a particular value that the pixels occupies. Set this way, there will be 256 such buckets per channel (for an 8 bit channel)

One particular question that we're interested in is with flattening the histogram. One way to do this is to compute the cumulative distribution function of the histogram and use it to determine the new intensity for each pixel

For instance, we can set $F(I) = C(I)$ where $F(I)$ is mapping intensity function and $C(I)$ is the cdf.

# References

1. Szeliski, R. (2023). Computer vision (2nd edn). Cham, Switzerland: Springer Nature.
